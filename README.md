

CHECKPOINT#4 
	
   		
 #PRODUCTSERVLET.JAVA
        

package com.iit.store.servlet;

import java.io.IOException;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.log4j.Logger;

import com.iit.store.service.StoreService;
import com.iit.store.util.StoreUtil;

@WebServlet(name="GetProductInfoServlet", urlPatterns={"/GetProductInfoServlet"})
public class GetProductInfoServlet extends HttpServlet {
	
	private static final long serialVersionUID = 1L;
       
	private static final Logger LOGGER = Logger.getLogger(GetProductInfoServlet.class);
	  
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		StoreService service = new StoreService();
		Map<String, Map<String, Object>> map = null;
		
		try {
			map = service.getProductInfoService();
		} catch (Exception e) {
			LOGGER.error("Unable to get Product Info", e);
		}
		
		response.setContentType("application/json");
		response.getWriter().print(StoreUtil.convertToJson(map));
	}

}







#USERINFOSERVLET.JAVA





package com.iit.store.servlet;

import java.io.IOException;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.log4j.Logger;

import com.iit.store.service.StoreService;
import com.iit.store.util.StoreUtil;

@WebServlet(name="GetUserInfoServlet", urlPatterns={"/GetUserInfoServlet"})
public class GetUserInfoServlet extends HttpServlet {
	
	private static final long serialVersionUID = 1L;
	
	private static final Logger LOGGER = Logger.getLogger(GetUserInfoServlet.class);
  
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		StoreService service = new StoreService();
		Map<String, Map<String, Object>> map = null;
		
		try {
			map = service.getUserInfoService();
		} catch (Exception e) {
			LOGGER.error("Unable to get User Info", e);
		}
		
		response.setContentType("application/json");
		response.getWriter().print(StoreUtil.convertToJson(map));
	}

}

# Initialize logj servlet.java
package com.iit.store.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.util.LinkedHashMap;
import java.util.Map;

import org.apache.log4j.Logger;

import com.iit.store.helper.ExceptionHandler;
import com.iit.store.util.ConnectionUtil;

public class StoreDao {
	
	private static final Logger LOGGER = Logger.getLogger(StoreDao.class);
	
	private PreparedStatement statement =  null;
	private ResultSet resultSet = null;
	
	
	public boolean dml(String query, Object[] parameters, Connection connection) {
		try {
			statement = connection.prepareStatement(query);
			if(parameters != null && parameters.length > 0) {
				for(int i=0; i<parameters.length; i++) {
					statement.setObject((i+1), parameters[i]);
				}
			}
			LOGGER.debug("Executing Query : " + query);
			if(statement.executeUpdate()>0) {
				return true;
			}
		} catch (Exception e) {
			ExceptionHandler.handle(e, "Exception while executing query", LOGGER, true);
		}
		return false;
	}
	
	public Map<String, Map<String, Object>> drl(String query, Object[] parameters, Connection connection) {
		
		Map<String, Map<String, Object>> mapList = null;
		int count = 0;
		
		try {
			statement = connection.prepareStatement(query);
			if(parameters != null && parameters.length > 0) {
				for(int i=0;i<parameters.length;i++){
					statement.setObject((i+1),parameters[i]);
				}
			}
			
			LOGGER.debug("Executing Query : " + query);
			resultSet = statement.executeQuery();
			
			mapList = new LinkedHashMap<String, Map<String,Object>>();
			
			while(resultSet.next()) {
				count++;
				Map<String, Object> map = new LinkedHashMap<String, Object>();
				ResultSetMetaData resultSetMetaData = resultSet.getMetaData();
				int colCount = resultSetMetaData.getColumnCount();
				String[] colNames = new String[colCount];
				for (int i = 0; i < colCount; i++) {
					colNames[i] = resultSetMetaData.getColumnName(i+1);
				}
				Object[] record = new Object[colNames.length];
				for (int i = 0; i < colNames.length; i++) {
					record[i] = resultSet.getObject(colNames[i]);
					map.put(colNames[i], record[i]);
				}
				mapList.put((count+""), map);
			}
		} catch (SQLException e) {
			ExceptionHandler.handle(e, "unable to execute query", LOGGER, true);
		} finally {
			ConnectionUtil.closeConnection(null, statement, resultSet);
		}
		return mapList;
	}
	
	public String userValidate(String query, String username, String password, Connection connection) {
		String role = null;
		try {
			statement = connection.prepareStatement(query);
			statement.setString(1, username);
			statement.setString(2, password);
			LOGGER.debug("Executing Query : " + query);
			resultSet = statement.executeQuery();
			if(resultSet.next()) {
				role = resultSet.getString("USER_TYPE");
			}
		} catch (Exception e) {
			ExceptionHandler.handle(e, "Execption while executing user validating query", LOGGER, true);
		} finally {
			ConnectionUtil.closeConnection(null, statement, resultSet);
		}
		return role;
	}
	
	public int getMaxIdValue(String query, Connection connection) throws Exception {
		int maxCount = -1;
		try {
			statement = connection.prepareStatement(query);
			LOGGER.debug("Executing Query : " + query);
			resultSet = statement.executeQuery();
			if(resultSet.next()) {
				maxCount = resultSet.getInt("MAXIDCOUNT");
			}
		} catch (Exception e) {
			ExceptionHandler.handle(e, "Execption while getting max id count value", LOGGER, true);
		} finally {
			ConnectionUtil.closeConnection(null, statement, resultSet);
		}
		return maxCount;
	}
	
	/*public static void main(String[] args) throws Exception {
		StoreDao dao = new StoreDao();
		Connection connection = null;
		try {
			connection = ConnectionUtil.getConnection();
			System.out.println(dao.getMaxIdValue(QueryConstant.MANUFACTURER_MAX_ID_COUNT_QUERY,connection));
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			ConnectionUtil.closeConnection(connection);
		}
	}*/
	
}

# Login validate servlet.java
package com.iit.store.servlet;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.apache.log4j.Logger;

import com.iit.store.constant.UserConstant;
import com.iit.store.service.StoreService;

@WebServlet(name="LoginValidateServlet", urlPatterns={"/LoginValidateServlet"})
public class LoginValidateServlet extends HttpServlet {

	private static final long serialVersionUID = 1L;
	
	private static final Logger LOGGER = Logger.getLogger(LoginValidateServlet.class);

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		String jspPage = "login";
        String user = request.getParameter("username");
        String pass = request.getParameter("password");
        
        LOGGER.debug("user : "+user);
        LOGGER.debug("password : "+pass);
        StoreService service = new StoreService();
        
        try {
        	HttpSession session = request.getSession();

        	String role = service.userValidateService(user, pass);
        	
        	LOGGER.debug("role : "+role);
        	        	
        	session.setAttribute("page", jspPage);
			
        	if(UserConstant.NORMAL_USER.equalsIgnoreCase(role)) {
				
			    session.setAttribute("user", user);
			    jspPage = "user";
			    session.setAttribute("page", jspPage);
			    
			} else if(UserConstant.ADMIN.equalsIgnoreCase(role)) {
				
			    session.setAttribute("user", user);
			    jspPage = "admin";
			    session.setAttribute("page", jspPage);
			    
			} else {
			    request.setAttribute("error", "Incorrect Username and Password");
			}
        	
		} catch (Exception e) {
			e.printStackTrace();
		}
        
        RequestDispatcher rd = request.getRequestDispatcher(jspPage);
        rd.forward(request, response);
        
	}

}

# Store dao.java
package com.iit.store.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.util.LinkedHashMap;
import java.util.Map;

import org.apache.log4j.Logger;

import com.iit.store.helper.ExceptionHandler;
import com.iit.store.util.ConnectionUtil;

public class StoreDao {
	
	private static final Logger LOGGER = Logger.getLogger(StoreDao.class);
	
	private PreparedStatement statement =  null;
	private ResultSet resultSet = null;
	
	
	public boolean dml(String query, Object[] parameters, Connection connection) {
		try {
			statement = connection.prepareStatement(query);
			if(parameters != null && parameters.length > 0) {
				for(int i=0; i<parameters.length; i++) {
					statement.setObject((i+1), parameters[i]);
				}
			}
			LOGGER.debug("Executing Query : " + query);
			if(statement.executeUpdate()>0) {
				return true;
			}
		} catch (Exception e) {
			ExceptionHandler.handle(e, "Exception while executing query", LOGGER, true);
		}
		return false;
	}
	
	public Map<String, Map<String, Object>> drl(String query, Object[] parameters, Connection connection) {
		
		Map<String, Map<String, Object>> mapList = null;
		int count = 0;
		
		try {
			statement = connection.prepareStatement(query);
			if(parameters != null && parameters.length > 0) {
				for(int i=0;i<parameters.length;i++){
					statement.setObject((i+1),parameters[i]);
				}
			}
			
			LOGGER.debug("Executing Query : " + query);
			resultSet = statement.executeQuery();
			
			mapList = new LinkedHashMap<String, Map<String,Object>>();
			
			while(resultSet.next()) {
				count++;
				Map<String, Object> map = new LinkedHashMap<String, Object>();
				ResultSetMetaData resultSetMetaData = resultSet.getMetaData();
				int colCount = resultSetMetaData.getColumnCount();
				String[] colNames = new String[colCount];
				for (int i = 0; i < colCount; i++) {
					colNames[i] = resultSetMetaData.getColumnName(i+1);
				}
				Object[] record = new Object[colNames.length];
				for (int i = 0; i < colNames.length; i++) {
					record[i] = resultSet.getObject(colNames[i]);
					map.put(colNames[i], record[i]);
				}
				mapList.put((count+""), map);
			}
		} catch (SQLException e) {
			ExceptionHandler.handle(e, "unable to execute query", LOGGER, true);
		} finally {
			ConnectionUtil.closeConnection(null, statement, resultSet);
		}
		return mapList;
	}
	
	public String userValidate(String query, String username, String password, Connection connection) {
		String role = null;
		try {
			statement = connection.prepareStatement(query);
			statement.setString(1, username);
			statement.setString(2, password);
			LOGGER.debug("Executing Query : " + query);
			resultSet = statement.executeQuery();
			if(resultSet.next()) {
				role = resultSet.getString("USER_TYPE");
			}
		} catch (Exception e) {
			ExceptionHandler.handle(e, "Execption while executing user validating query", LOGGER, true);
		} finally {
			ConnectionUtil.closeConnection(null, statement, resultSet);
		}
		return role;
	}
	
	public int getMaxIdValue(String query, Connection connection) throws Exception {
		int maxCount = -1;
		try {
			statement = connection.prepareStatement(query);
			LOGGER.debug("Executing Query : " + query);
			resultSet = statement.executeQuery();
			if(resultSet.next()) {
				maxCount = resultSet.getInt("MAXIDCOUNT");
			}
		} catch (Exception e) {
			ExceptionHandler.handle(e, "Execption while getting max id count value", LOGGER, true);
		} finally {
			ConnectionUtil.closeConnection(null, statement, resultSet);
		}
		return maxCount;
	}
	
	/*public static void main(String[] args) throws Exception {
		StoreDao dao = new StoreDao();
		Connection connection = null;
		try {
			connection = ConnectionUtil.getConnection();
			System.out.println(dao.getMaxIdValue(QueryConstant.MANUFACTURER_MAX_ID_COUNT_QUERY,connection));
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			ConnectionUtil.closeConnection(connection);
		}
	}*/
	
}

# Exception handler.java
package com.iit.store.helper;

import org.apache.log4j.Logger;


public class ExceptionHandler {
    
    public static Error handle(Throwable t, String message, Logger logger, boolean reThrowError) throws Error {
    
    	try {
    		t.printStackTrace();
            logger.error(message, t);
            if (reThrowError) {
                throw new Error(t);
            }
        }
        catch (Throwable innerThrowable) {
            logger.error("severe while handling Exception : " + innerThrowable.getMessage(), innerThrowable);
        }
    	return null;
    }
}

