# checkpoint-3
#Some of the classes in our project
STORESERVLET
#ManufacturerServlet.java
package com.iit.store.servlet;

import java.io.IOException;
import java.util.LinkedHashMap;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.log4j.Logger;

import com.iit.store.service.StoreService;
import com.iit.store.util.StoreUtil;

@WebServlet(name="AddManufacturerServlet", urlPatterns={"/AddManufacturerServlet"})
public class AddManufacturerServlet extends HttpServlet {

	private static final long serialVersionUID = 1L;
    
	private static final Logger LOGGER = Logger.getLogger(AddManufacturerServlet.class);
	
   	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   		
   		String name = request.getParameter("mname");

   		
   		Map<String, String> map = new LinkedHashMap<String, String>();
   		map.put("result", "manufacturer registration failed");
   		
   		StoreService service = new StoreService();

   		try {
   			if(service.registerManufacturerService(name)) {
   				LOGGER.debug("manufacturer registered successfully");
   				map.put("result", "manufacturer registered successfully");
   			}
   		} catch (Exception e) {
   			LOGGER.error("manufacturer registration failed",e);
   		}
   	   	
   		
   		
   		response.setContentType("application/json");
		response.getWriter().print(StoreUtil.convertToJson(map));
	}
}
#ProductServlet.java
package com.iit.store.servlet;

import java.io.IOException;
import java.util.LinkedHashMap;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.log4j.Logger;

import com.iit.store.service.StoreService;
import com.iit.store.util.StoreUtil;

@WebServlet(name="AddProductServlet", urlPatterns={"/AddProductServlet"})
public class AddProductServlet extends HttpServlet {

	private static final long serialVersionUID = 1L;
    
	private static final Logger LOGGER = Logger.getLogger(AddProductServlet.class);
	
   	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   		
   		String name = null;
		String desc = null;
		String color = null;
		float weight = 0;
		int quantity = 0;
		float price = 0;
		int mid = 0;
		int status = 0;
		
		name =  request.getParameter("pname");
		desc =  request.getParameter("pdesc");
		color =  request.getParameter("pcolor");
		weight =  Float.parseFloat(request.getParameter("pweight"));
		quantity =  Integer.parseInt(request.getParameter("pquantity"));
		price =  Float.parseFloat(request.getParameter("pprice"));
		mid =  Integer.parseInt(request.getParameter("mid"));
		status =  Integer.parseInt(request.getParameter("statusType"));
		
		StoreService service = new StoreService();
		
		Map<String, String> map = new LinkedHashMap<String, String>();
   		map.put("result", "product registration failed");
   		
        try {
        	
        	if(service.registerProductService(name, desc, color, weight, quantity, price, mid, status)) {
   				LOGGER.debug("product registered successfully");
   				map.put("result", "product registered successfully");
   			}
           	
        } catch(Exception e) {
        	LOGGER.error("product registration failed",e);
        }
   	   	
   		response.setContentType("application/json");
		response.getWriter().print(StoreUtil.convertToJson(map));
	}

}
#UserServlet.java
package com.iit.store.servlet;

import java.io.IOException;
import java.util.LinkedHashMap;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.apache.log4j.Logger;

import com.iit.store.service.StoreService;
import com.iit.store.util.StoreUtil;

@WebServlet(name="AddUserServlet", urlPatterns={"/AddUserServlet"})
public class AddUserServlet extends HttpServlet {

	private static final long serialVersionUID = 1L;
	
	private static final Logger LOGGER = Logger.getLogger(AddUserServlet.class);
       
   	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   		
   		String username = request.getParameter("uname");
   		String password = request.getParameter("upass");
   		String cpassword = request.getParameter("ucpass");
   		String userType = request.getParameter("userType");
   		
   		Map<String, String> map = new LinkedHashMap<String, String>();
   		map.put("result", "user registration failed");
   		
   		if(password.equalsIgnoreCase(cpassword)) {
   			
   			StoreService service = new StoreService();
   	   		
   	   		try {
   				if(service.registerUserService(username, password, userType)) {
   					LOGGER.debug("user registered successfully");
   					map.put("result", "user registered successfully");
   				}
   			} catch (Exception e) {
   				LOGGER.error("user registration failed",e);
   			}
   	   	
   		} else {
   			map.put("result", "password and confirm password should be same");
   		}
   		
   		response.setContentType("application/json");
		response.getWriter().print(StoreUtil.convertToJson(map));
	}

}
#ManufacturerInfoServlet.java
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

@WebServlet(name="GetManufacturerInfoServlet", urlPatterns={"/GetManufacturerInfoServlet"})
public class GetManufacturerInfoServlet extends HttpServlet {
	
	private static final long serialVersionUID = 1L;
       
	private static final Logger LOGGER = Logger.getLogger(GetManufacturerInfoServlet.class);
	  
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		
		StoreService service = new StoreService();
		Map<String, Map<String, Object>> map = null;
		
		try {
			map = service.getManufacturerInfoService();
		} catch (Exception e) {
			LOGGER.error("Unable to get Manufacturer Info", e);
		}
		
		response.setContentType("application/json");
		response.getWriter().print(StoreUtil.convertToJson(map));
	}

}
