+Web
    |---------------- index.jsp ǰ����ת��ҳ
    |---------------- images ͼƬĿ¼
    |---------------- +META-INF Ӧ��װ�������ļ�
    |                                   |---------- context.xml ���ڴ�Ӧ�õ����������ã�����������ʹ�� Tomcat ����Դ������(����ʱ���
    |---------------- WEB-INF

Ӧ������˵��
============

# ================ Mysql ���ݿ��ʼ�� =================

# ������������ root �������
C:\Documents and Settings\pprun > mysql -h localhost -u root -p < $hjpetstore\conf\jpetstore_mysql.sql
Enter password: ********


# ======== ��ͬӦ�÷�����/web�����������ݿ���л� ========

1. ��򵥵ķ�ʽ����ʹ����Ĭ������(DBCP �������ӳ�) tomcat + HSQLDB, ���������䣬
��������Ʒʱ���ǵ�ʹ������Ӧ�÷����������ݿ�ġ�

2. ʹ�� tomcat+mysql Ӧ��˵���ǱȽ����ף�ֻҪ��jdbc.properties �ļ��н���Ӧ��
    ������þ��У�Ψһ������ĵط���ÿ�����²���ĵ�һ�����ǲ��ɹ��ġ�
�������Դ�������һ�Σ��Ϳ����ˡ�

3. ʹ�� tomcat + oracle ͬ�ϣ�ֻҪ�� ORCL �Ĳ����� jdbc.properties �����úþ��С�

4. ʹ�� Sun App Server ��Ŀ�ĵ�Ȼ��Ϊ��ʹ���� JTA(��������������������ݿ�����
   �ص�ʵ�֣���ֻ��Ҫ����ȷ�����ơ�(mysql)jdbc/hjpetstore 
   �ڹ������������ݿ����ӳغ���Ӧ������Դ��������������ͦ�����,
   ������ web.xml �а���׼�ķ�ʽ������
  <resource-ref>
    <res-ref-name>jdbc/hjpetstore</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
  </resource-ref>
  
   �⣬IDE�����Լ������� sun-web.xml�ļ������������У�
  <resource-ref>
    <res-ref-name>jdbc/jpetstore-mysql</res-ref-name>
    <jndi-name>jdbc/jpetstore-mysql</jndi-name>
  </resource-ref>
 ������Դ���ö���.

5. ʹ�� JBoss ��ȻҲ��Ϊ������ͬ����Ŀ�ģ�Ҳ���ǳ�����ν�� #1 Ӧ�÷����������İɣ�
��������������һЩ�仯��
   ��һ��ʵ�����Լ���һ����־��ʽ��������Ҫ�� web.xml �е�
   <listener>
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
  </listener>
  ע�͵���

6. ��������Դ���䷨Ҳ����ͬ��ֻ��Ҫ����Ӧ�����ݿ�������ļ�(�磺mysql-ds.xml)���� 
   jboss-4.0.4.GA\server\default\deploy Ŀ¼�£�
   ���� jboss-4.0.4.GA\server\default\conf\login-config.xml �м���:
  
    <application-policy name = "MySqlDbRealm">
      <authentication>
         <login-module code = "org.jboss.resource.security.ConfiguredIdentityLoginModule" flag = "required">
             <module-option name ="principal">hjpetstore</module-option>
             <module-option name ="userName">hjpetstore</module-option>
             <module-option name ="password">hjpetstore</module-option>
             <module-option name ="managedConnectionFactoryName">jboss.jca:service=LocalTxCM,name=hjpetstore-mysql</module-option>
         </login-module>
      </authentication>
    </application-policy>

    mysql-ds.xml ��Ӧ���������£�
    <?xml version="1.0" encoding="UTF-8"?>
    <!-- $Id: readme.txt,v 1.1.1.1 2010/07/16 07:47:08 pprun Exp $ -->
    <!--  Datasource config for MySQL using 3.0.9 available from:
    http://www.mysql.com/downloads/api-jdbc-stable.html
    -->

    <datasources>
      <local-tx-datasource>
        <jndi-name>hjpetstore-mysql</jndi-name>
        <connection-url>jdbc:mysql://localhost:3306/hjpetstore</connection-url>
        <driver-class>com.mysql.jdbc.Driver</driver-class>
        <user-name>hjpetstore</user-name>
        <password>hjpetstore</password>
        <exception-sorter-class-name>org.jboss.resource.adapter.jdbc.vendor.MySQLExceptionSorter</exception-sorter-class-name>
        <!-- should only be used on drivers after 3.22.1 with "ping" support
        <valid-connection-checker-class-name>org.jboss.resource.adapter.jdbc.vendor.MySQLValidConnectionChecker</valid-connection-checker-class-name>
        -->
        <!-- sql to call when connection is created
        <new-connection-sql>some arbitrary sql</new-connection-sql>
          -->
        <!-- sql to call on an existing pooled connection when it is obtained from pool - MySQLValidConnectionChecker is preferred for newer drivers
        <check-valid-connection-sql>some arbitrary sql</check-valid-connection-sql>
          -->

        <!-- corresponding type-mapping in the standardjbosscmp-jdbc.xml (optional for ejb) -->
        <metadata>
           <type-mapping>mySQL</type-mapping>
        </metadata>
      </local-tx-datasource>
    </datasources>

7. ���У�����JBoss �� JNDI ��������Щ�֣�

��<!-- JBoss must be this -->
  <jee:jndi-lookup id="dataSource" jndi-name="java:/hjpetstore-mysql"/>

���������Ϊ��
  <jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/hjpetstore-mysql"/>
  
  ����Ȳ���Ҫ�� web.xml�ж��壬Ҳ����Ҫ�� jboss-web.xml �ж�����������Դ���ã�1�7
  �о�����֣��ǲ�������Ĳ�����ɺ�Ͳ���Ҫ����Щ�ˣ�


ʹ�� jta����������������Դʱ�����л�����
----------------------------------------
1. Sun App Server + mysql
 a. web.xml
�������ڣ� 
 -- 
    <resource-ref>
    <res-ref-name>jdbc/hjpetstore</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
   </resource-ref> 
 --
  <listener>
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
  </listener>

 --     <!-- jta DataSource and JtaTransactionManager -->
    <param-value>
      /WEB-INF/dataAccessContext-jta-1.xml  /WEB-INF/applicationContext.xml
    </param-value>
 b. sun-web.xml �д��ڣ�
   <resource-ref>
    <res-ref-name>jdbc/hjpetstore-mysql</res-ref-name>
    <jndi-name>jdbc/hjpetstore-mysql</jndi-name>
  </resource-ref>

 c. dataAccessContext-jta-1.xml ��ʹ�ã�
   <jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/hjpetstore-mysql"/>
   �����ǡ�
  <!-- JBoss must be this -->
  <jee:jndi-lookup id="dataSource" jndi-name="java:/hjpetstore-mysql"/>
  
2. JBoss + mysql
 a. web.xml
 --��ע�͵�����������Դ��ص�Ԫ�أ� 
     <resource-ref> ... </resource-ref>

 --  ע����־������
  <listener>
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
  </listener>

 -- ͬ����֤ʹ�ã�
 ��<!-- jta DataSource and JtaTransactionManager -->
    <param-value>
      /WEB-INF/dataAccessContext-jta-1.xml  /WEB-INF/applicationContext.xml
    </param-value>

 b. dataAccessContext-jta-1.xml �в���ʹ�ã�
   <jee:jndi-lookup id="dataSource" jndi-name="java:comp/env/jdbc/hjpetstore-mysql"/>
   ����ʹ�ã���
  <!-- JBoss must be this -->
  <jee:jndi-lookup id="dataSource" jndi-name="java:/hjpetstore-mysql"/>

3. Oracle ��صĲ���������ͬ��ֻ�ǡ�
��������Դ���õ�ʱ��Ϊoracle �����ã���Ϊ���������������, -mysql Ϊ��׺�ļ���
 mysql ��أ�����Ϊ oracle ���


���
====
JBoss NO. 1 ��ͽ�������𣿲����о���ͬ���������£���ȷҪ�� Sun App server ��Щ��

############# �� Spring web �� Struts web ���л�############### 
1. ԭӦ��ֻ�轫
  <servlet-mapping>
    <!-- Spring web -->
    <servlet-name>petstore</servlet-name>
    
    <!-- Struts web 
    <servlet-name>action</servlet-name>
    -->	
   <url-pattern>*.do</url-pattern>
  </servlet-mapping>
 ��Ԫ������Ϊ�Լ�ϲ���Ŀ�ܾͿɣ����� Spring ��������ͼӳ����Ҳ������ *.do 
 ����ԭ���� Struts �����ĺ�׺��ӳ�䷽ʽ

 2. ��ͨ�� Spring ��ӳ�䷽ʽ�ǲ��� .htm/.html ��ʽ������������һ�¸Ķ�
 - �� Spring ��ҳ���е� .do , �ĳ��� *.html
 - ��  petstore-servlet-xml �е����� .do �ĳ��� .html

 ���ҷֱ��ڸ��Ե�ҳ���������ת���Է�������, �����Ϳ��Զ�̬���������֮���л�

 ��������������Ļ������þ������£�
a.
  <servlet-mapping>
    <!-- Spring web -->
    <servlet-name>petstore</servlet-name>
    <url-pattern>*.html</url-pattern>
  </servlet-mapping>
  
  <!-- Struts web -->	
  <servlet-mapping>
    <servlet-name>action</servlet-name>
    <url-pattern>*.do</url-pattern>
  </servlet-mapping>


  ע�����ʹ�����Լ���ϰ�ߵ�ӳ�䷽ʽ

 b.�� WEB ���£�����һ��ҳ index.jsp, �������£�
<%-- for ��Ʒ��ҳ --%>
<%@page contentType="text/html; charset=GBK"%>
<%@page pageEncoding="GBK"%>
<%@ page session="false"%>
<%@ taglib uri="http://java.sun.com/jstl/core" prefix="c" %>

<%-- selecting the default web framework --%>
<%-- Spring web --%>
<c:redirect url="/shop/index.html"/>

<%-- Struts web 
<c:redirect url="/shop/index.do"/>
--%>

 ����Ҳ��������ԭ����index.html ��˵����ֱ����ת��Ĭ�Ͽ�������


