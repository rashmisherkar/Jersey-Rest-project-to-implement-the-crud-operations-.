# Jersey-Rest-project-to-implement-the-crud-operations-.
simple Jersey Rest project to implement the crud operations for the Student table.



1. Configure Java Dynamic Web Project for RESTful Web Services with Jersey

*pom.xml file:


  <dependencies>
      <dependency>
            <groupId>org.glassfish.jersey.containers</groupId>
            <artifactId>jersey-container-servlet</artifactId>
            <version>2.29.1</version>
  </dependency>
  <dependency>
            <groupId>org.glassfish.jersey.inject</groupId>
            <artifactId>jersey-hk2</artifactId>
            <version>2.29.1</version>
  </dependency>
  <dependency>
           <groupId>org.glassfish.jersey.media</groupId>
           <artifactId>jersey-media-json-jackson</artifactId>
           <version>2.29.1</version>
   </dependency>
   <dependency>
           <groupId>org.glassfish.jaxb</groupId>
           <artifactId>jaxb-runtime</artifactId>
           <version>2.3.2</version>
</dependency>
 </dependencies>   
    


*web.xml file

  <servlet>
      <servlet-name>Jersey REST Service</servlet-name>
      <servlet-class>org.glassfish.jersey.servlet.ServletContainer</servlet-class>
  
	<init-param>
         <param-name> jersey.config.server.provider.package</param-name>
         <param-value>net.code.ws</param-value>
     </init-param>
    
	<load-on-startup>1</load-on-startup> 
   </servlet>
    
    <servlet-mapping>
          <servlet-name>Jersey REST Service</servlet-name>
         <url-pattern>/rest/*</url-pattern>
      </servlet-mapping>   
  
</web-app>


2. Code Domain Model Class

*Student.java

package net.codejava.ws;

public class Student {
	private int studentno;
	private String studentName;
	private String studentDOB;
	private String studentDOJ;
	
	
	public Student(int studentno) {
		
		this.studentno = studentno;
	}
	
	public Student(int studentno,String studentName, String studentDOB, String studentDOJ) {
		this.studentno = studentno;
        this.studentName = studentName;
		this.studentDOB = studentDOB;
		this.studentDOJ = studentDOJ;
	}
	
	
	public int getStudentno() {
		return studentno;
	}
	public void setStudentno(int studentno) {
		this.studentno = studentno;
	}
	public String getStudentName() {
		return studentName;
	}
	public void setStudentName(String studentName) {
		this.studentName = studentName;
	}
	public String getStudentDOB() {
		return studentDOB;
	}
	public void setStudentDOB(String studentDOB) {
		this.studentDOB = studentDOB;
	}
	public String getStudentDOJ() {
		return studentDOJ;
	}
	public void setStudentDOJ(String studentDOJ) {
		this.studentDOJ = studentDOJ;
	}
	
	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + studentno;
				//((studentDOB == null) ? 0 : studentDOB.hashCode());
		return result;
	}
	
	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Student other = (Student) obj;
		
		if  (studentno!= other.studentno)
			return false;
		return true;
	}

	@Override
	public String toString() {
		return "Student [studentno=" + studentno + ", studentName=" + studentName + ", studentDOB=" + studentDOB
				+ ", studentDOJ=" + studentDOJ + "]";
	}
}

3. Code DAO Class

*StudentDAO.java

package net.codejava.ws;

import java.util.ArrayList;
import java.util.List;

public class StudentDAO {
	private static StudentDAO instance;
    private static List<Student> data = new ArrayList<>();
    
    static {
    	 data.add(new Student(1,"Rashmi","15/05/1994","20/03/2020"));
    	 data.add(new Student(2,"Shilpa","08/07/1997","04/02/2021"));
    }
    private StudentDAO() {}
    
	public static StudentDAO getInstance() {
		if(instance == null) {
			instance = new StudentDAO();
		}
		return instance;
		
	}

	public List<Student> listAll(){
		return new ArrayList<Student>(data);
	}
	
	public int add(Student student) {
		int newStudentNo = data.size() + 1;
		student.setStudentno(newStudentNo);
		data.add(student);
		
		return newStudentNo;
		
	}

	public Student get(int studentno) {
		Student studentToFind = new Student(studentno);
        int index = data.indexOf(studentToFind);
        if(index >= 0) {
        	return data.get(index);  	
        }
           return null;        
	}
	
	public boolean update(Student student) {
		int index = data.indexOf(student);
		if(index >= 0) {
			data.set(index, student);
			return true;
		}
		return false;
	}

	public boolean delete(int studentno) {
		Student studentToFind = new Student(studentno);
        int index = data.indexOf(studentToFind);
        if(index >= 0) {
            data.remove(index); 
            return true;
        }
           return false;
	}
	
}

4. Code CRUD RESTful Web Services Class

  *StudentResource.java
  
  package net.codejava.ws;

import java.net.URI;
import java.net.URISyntaxException;
import java.util.List;

import javax.ws.rs.Consumes;
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.PUT;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;

@Path("/student")

public class StudentResource { 
   private StudentDAO dao = StudentDAO.getInstance();
   
1...Code RESTful API method for Retrieval (list all student):
  
   @GET
   @Produces(MediaType.APPLICATION_JSON)
   public List<Student> list() {
   return dao.listAll();
   }
  
  OUTPUT:
  cmd:
  curl http://localhost:8080/MyWebsite/rest/student
  
  from server: [{"studentno":1,"studentName":"Rashmi","studentDOB":"15/05/1994","studentDOJ":"20/03/2020"},
  {"studentno":1,"studentName":"Shilpa","studentDOB":"08/07/1997","studentDOJ":"04/02/2021"}]
  
2....Code RESTful API method for Retrieval (get a specific item):
  
   @GET
   @Path("{studentno}")
   @Produces(MediaType.APPLICATION_JSON)
   public Response get(@PathParam("studentno") int studentno) {
       Student student = dao.get(studentno);
       if (student != null) {
           return Response.ok(student, MediaType.APPLICATION_JSON).build();
       } else {
           return Response.status(Response.Status.NOT_FOUND).build();
       }
   }
  
  OUTPUT:
    cmd:
  curl http://localhost:8080/MyWebsite/rest/student/1
   
  from server: [{"studentno":1,"studentName":"Rashmi","studentDOB":"15/05/1994"}]
  
 3....Code RESTful API method for Create (add a new student):
  
   @POST
   @Consumes(MediaType.APPLICATION_JSON)
   public  Response add(Student student) throws URISyntaxException {
	   int newStudentNo =dao.add(student);
	   URI uri = new URI("/student/" + newStudentNo);
	   return Response.created(uri).build();
   }
  
  OUTPUT:
  curl -v -X POST -H "Content-Type: application/json"
  -d  "{\"studentName\":\"Snehal\",\"studentDOB\":\"20/03/1995\",\"studentDOJ\":\"20/02/2019\"}"
 http://localhost:8080/MyWebsite/rest/Student/

  HTTP/1.1 201
Location: http://localhost:8080/student/3
Content-Length: 0

  
4....Code RESTful API method for Update (update an existing student):
  
   @PUT
   @Consumes(MediaType.APPLICATION_JSON)
   @Path("{studentno}")
   public Response update(@PathParam("studentno") int studentno, Student student) {
       student.setStudentno(studentno);
       if (dao.update(student)) {
           return Response.ok().build();
       } else {
           return Response.notModified().build();
       }
   }
  
  OUTPUT:
  curl -v -X PUT -H "Content-Type: application/json"
    -d  "      {\"studentName\":\"Rohit\",\"studentDOB\":\"05/03/1998\",\"studentDOJ\":\"20/02/2019\"}"
 http://localhost:8080/MyWebsite/rest/Student/3
  
  HTTP/1.1 200
Content-Length: 0
  
5....Code RESTful API method for Delete (remove an Student):
  
    @DELETE
   @Path("{studentno}")
   public Response delete(@PathParam("studentno") int studentno) {
       if (dao.delete(studentno)) {
           return Response.ok().build();
       } else {
           return Response.notModified().build();
       }
   }
  
 OUTPUT:
  
 curl -v -X DELETE http://localhost:8080/MyWebsite/rest/student/1
  
  
  5. Code RESTful Web Services Client Program
  
   pom.xml file:
   
<dependencies>

<dependency>
      <groupId>org.glassfish.jersey.core</groupId>
      <artifactId>jersey-client</artifactId>
      <version>2.29.1</version>
</dependency>

<dependency>
      <groupId>org.glassfish.jersey.inject</groupId>
      <artifactId>jersey-hk2</artifactId>
     <version>2.29.1</version>
  </dependency>

<dependency>
      <groupId>org.glassfish.jersey.media</groupId>
      <artifactId>jersey-media-json-jackson</artifactId>
      <version>2.29.1</version>
</dependency>

<dependency>
     <groupId>org.glassfish.jaxb</groupId>
     <artifactId>jaxb-runtime</artifactId>
      <version>2.3.2</version>
 </dependency>

</dependencies>  

  
  
*StudentClient.java
package net.codejava.client;

import javax.ws.rs.client.*;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;

import org.glassfish.jersey.client.ClientConfig;
 
public class StudentClient {

    private static String baseURI = "http://localhost:8080/MyWebsite/rest/student";
  
   static WebTarget getWebTarget() {
    ClientConfig config = new ClientConfig();
    Client client = ClientBuilder.newClient(config);
      return client.target(baseURI);
 
    }  
    public static void main(String[] args) {
      //testList();
      //testGet();
    	//testAdd();
    	//testUpdate();
    	//testDelete();
    	//testList();
    }
  }
  
 ** To test listing all Student:**
    
  static void testList() {
    	WebTarget target = getWebTarget(); 
    	String response = target.request()
         		.accept(MediaType.APPLICATION_JSON)
         		.get(String.class);
         	
         System.out.println(response);
    }
  
  
   ** To get specific Student by no:**
  
  	static void testGet() { 
    	WebTarget target = getWebTarget();
    	String studentno = "1";
    	Student student = target.path(studentno)
    	                       .request()
    	                       .accept(MediaType.APPLICATION_JSON)
    	                        .get(Student.class);
        System.out.println(student);

    }
  
  **Test to add a new student**
  
      static void testAdd() {
    	WebTarget target = getWebTarget();
        Student student = new Student(3,"Snehal","20/03/1995","20/02/2019");
       Response response = target.request().post(
        		Entity.entity(student,MediaType.APPLICATION_JSON),
        		Response.class);
       
        System.out.println(response.getLocation().toString());
    }
  
   **Test to update student**
    
   private static void testUpdate() {
    	WebTarget target = getWebTarget();
        Student student = new Student(3,"Rohit","05/03/1998","12/12/2020");
         String studentno = "3";
         Response response = target.path(studentno)
        		 .request()
    	         .put(Entity.entity(student, MediaType.APPLICATION_JSON), Response.class);
         System.out.println(response);
    	}
  
   **Test to Delete student**
  
  
  	private static void testDelete() {
	    WebTarget target = getWebTarget();
    	String studentno = "1";
    	
    	Response response = target.path(studentno)
                   .request()
	            .delete(Response.class);
	    System.out.println(response);      

 }
	
  
    static WebTarget getWebTarget() {
    ClientConfig config = new ClientConfig();
    Client client = ClientBuilder.newClient(config);
      return client.target(baseURI);
 
    }
}

  

  
  
