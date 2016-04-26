

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

