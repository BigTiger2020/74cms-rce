# 74cms Remote Code Execution Vulnerability  
* Vulnerability Type :  
Remote Code Execution  
* Vulnerability Version :  
74CMS = v5.0.1   
* Recurring environment:  
Windows 10  
PHP 5.6.9  
Apache 2.4.23  
* Vulnerability analysis  
1. c=config&a=edit -> Controller=config&action=edit -> /Application/Admin/Controller/ConfigController.class.php Line 9: I('request.site_domain','','trim') -> trim($site_domain,'/') -> '.'.implode('.',$domain) -> array('domain'=>$domain) -> $this->update_config($config,CONF_PATH.'url.php')
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/05.png)    

2. I('request.site_domain','','trim') in /ThinkPHP/Common/functions.php Line 271: request.site_domain -> $_REQUEST['site_domain'] ->$_REQUEST['site_domain'] is string -> trim($_REQUEST['site_domain'] )  
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/06.png)    

3. back to /Application/Admin/Controller/ConfigController.class.php Line 29: array('SESSION_OPTIONS' => array('domain'=>$domain), 'COOKIE_DOMAIN' => $domain) -> update_config($config,CONF_PATH.'url.php')  
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/07.png)    

4. update_config($config,CONF_PATH.'url.php') in /Application/Common/Controller/BackendController.class.php Line 467:
multimerge($config, $new_config) -> file_put_contents($config_file, "<?php \nreturn " . stripslashes(var_export($config, true)) . ";", LOCK_EX)  
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/08.png)     

5. multimerge($config, $new_config) in /Application/Common/Common/function.php Line 938: no restricted  
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/09.png)    

6. CONF_PATH in /ThinkPHP/ThinkPHP.php Line 54: CONF_PATH.'url.php' -> /Application/Common/Conf/url.php  
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/11.png)   

7. var_export(): Quote string with slashes&Convert special characters to HTML entities -> stripslashes(): un-quotes a quoted string -> Convert special characters to HTML entities -> write file  
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/12.png)  

8. /Application/Home/Conf/url.php: The code after "return array(...);" does not work, so payload is site_domain=', {your php code},'  
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/10.png)    

* POC
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/13.png) 
* Steps to reproduce
1.   
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/04.png)   
2.   
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/01.png)   
3.    
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/03.png)   
4.    
![image](https://github.com/BigTiger2020/74cms-rce/blob/main/02.png) 


