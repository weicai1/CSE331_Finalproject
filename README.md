# CSE331_Finalproject
CSE331 2018 fall Finalproject
Nickname: Human=Repeater
Number of members: 4
Members: 
Chongxuan Yin (chongxuan.yin@stonybrook.edu), 
Wei Cai (wei.cai@stonybrook.edu), 
Xu Wu (xu.wu@stonybrook.edu), 
Xiyun Xie(xiyun.xie@stonybrook.edu)
Links towards the Github/Bitbucket repository:
https://github.com/weicai1/CSE331_Finalproject
Project Number: Project 1: 


Web Application Firewall


Application Instruction
Preparing (for Windows)
1. Install python in your computer and setup python. Relative link: https://www.python.org/downloads/
2. Install mitmproxy. Relative link: https://docs.mitmproxy.org/stable/overview-installation/
3. Install Chrome browser.
4. Import certificate “mitmproxy-ca-cert.p12” we give into Chrome browser for Windows system, “mitmproxy-ca-cert.pem” for mac or Linux.
Start Chrome browser -- Settings – Advanced (At the bottom) – Manage certificates – Import – Next – Import “mitmproxy-ca-cert.p12”
5. Make sure you have other browsers like Firefox.
6. Make sure you have closed all extensions in Chrome, otherwise the certificate you install may not work.
7. Close all Chrome tabs and windows.

Training Phase
1. Open command prompt.
2. Go to the directory which contains our python files.
3. Type: mitmweb -s train.py in IDEA or terminal.
You will see: 
Web server listening at http://127.0.0.1:8081/
Loading script train.py
Proxy server listening at http://*:8080
And it opens a webpage “mitmproxy”
4. Make sure the webpage is not on Chrome because you will need Chrome as the client. The additional browser acts as the listener. If Chrome is opened, cope the link, close Chrome, open other browser, and paste the link.
5. Open another command prompt.
6. Type: "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" --proxy-server=127.0.0.1:8080 --ignore-certificate-errors
(The path is the address of “chrome.exe” installed in Your PC)
And it opens Chrome with ignoring the certificate error.
7. Try open webpages like “www.google.com”, “joomla.org” or www.facebook.com as many as possible in the Chrome. You can see that requests are recorded in the listener browser.
8. If you want to stop training, close Chrome and type “Ctrl+C” to stop the program. 

Online Phase
1. Type: mitmweb -s online.py
You will see: 
Web server listening at http://127.0.0.1:8081/
Loading script online.py
Proxy server listening at http://*:8080
And it opens a webpage “mitmproxy”
2. Make sure the webpage is not on Chrome. If it is, cope the link, close Chrome, open other browser, paste and open the link.
3. Open another command prompt.
4. Type: "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" --proxy-server=127.0.0.1:8080 --ignore-certificate-errors
(The path is the address of “chrome.exe” installed in your PC)
And it opens Chrome.
5. You can open webpages that contains intentional error in the URL and test if Web Application Firewall works.





How we did and what we did
Preparing
1. Read the Project description.
2. Google what is mitmproxy.
3. Follow the tutorial online and set up mitmproxy environment. Read simple code to learn how it works and function usage.
4. Test code example on the webpage in our own python IDEA by importing a mitmproxy model. 
5. Try some simple functions such as request, response and http_connect so that we can learn how to write mitmproxy code on Python. Such as banning www.google.com

Part 1. Signatures
1. 
 
Use flow.request.headers to get the User_Agent and check whether it contains “<script>” or “bot”
2.
 
Use flow.request.method to get method “GET”. Use urlparse to parse the value and get the query, which will return a dictionary of parameters and their values. Check whether the parameters contain “union all select”
3.
 
Use flow.request.method to get method “POST”. Use urlparse to parse the content of the body and get the query, which will return a dictionary of parameters and their values. Check whether the dictionary has a key of “foo” with value “../../../../”
If any of the situations above happens in the online phase, we will automatically replace the response as “Firewall: Detect an Anomaly”.


Part 2.a Training Phase
RULE 1
 
1. Build a databse by importing shelve model. 
2. When users click all links, record the length of each paramater and save the maximum length of parameters in the file “cse331_database”


RULE 2
 
1. Build a databse by importing shelve model. 
2. When users click a page, record the path of the page by parsing the url and get the netloc+path to form a path of the page. This path represents the string excluding parameters.
3. Take each path as the key and save the key into file “mnofpfesp”. Take the maximum length of each page as the value of the key and save the value into database “mnofpfesp”  


RULE 3
 
1. Build a databse by importing json model.  
2. When users click a page, record the path of the page by parsing the url and get the netloc+path to form a path of the page. This path represents the string excluding parameters.
3. Take each parameter combining its path as the key in the body like “www.googleapis.com/oauth2/v4/token_client_id” and save the key into file “db4.json”. The value of a key is a list, which contains the length of value of a parameter.




RULE 4
 
1. Build a databse by json.  
2. When users click a page, record the path of the page by parsing the url and get the netloc+path to form a path of the page. This path represents the string excluding parameters.
3. Take each parameter combining its path as the key in the body like “www.googleapis.com/oauth2/v4/token_client_id” and save the key into file “db3.json”. 
4. Check whether the value of each parameter contains special characters. If “yes”, save “1”. Otherwise save “0”.



Part 2.b Online Phase
RULE 1
Check whether the length of each parameter in the URL or body exceeds the maximum number of all parameters which is saved in the file “cse331_database”. If “yes”, We return a 403 page with text “Firewall: Detect an Anomaly. drop requests that exceed that maximum number”.
For example, if the value in the file “cse331_database” is 5, and there is a parameter with length 6 or more in a request URL. We return a 403 page with text “Firewall: Detect an Anomaly. drop requests that exceed that maximum number”. We are ignoring number of requests and whether their host name or path is the same or not. 

RULE 2
Check whether the length of each parameter in the URL or body of every specific page exceeds the value in the file “mnofpfesp” with path as the key. If “yes”, We return a 403 page with text “Firewall: Detect an Anomaly. drop requests that exceed that maximum number for a specific page”.
For example, if “www.facebook.com” has a parameter with length 100, and the value of “www.facebook.com” in the file “mnofpfesp” is 50. We return a 403 page with text “Firewall: Detect an Anomaly. drop requests that exceed that maximum number for a specific page”.


RULE 3
Take averages and standard deviation of a list of values for each parameter in the URL or body of every specific page in the file “db4.json”. In order to avoid too much false positive, we set minimum sd to be 1/10 of average when the page is opened less than 5 times. Check whether the length of each value of each parameter in the URL or body of every specific page exceeds (average+-3*sd). If “yes”, We return a 403 page with text “Firewall: Detect an Anomaly. drop all values that are not part of the mean+-3*sd”.
For example, if “www.facebook.com” has a parameter “username” of length 30, and average length and standard deviation with the key “www.facebook.com_username” in the file “db4.json” is 15 and 4. We return a 403 page with text “Firewall: Detect an Anomaly. drop all values that are not part of the mean+-3*sd”.

RULE 4
Take Boolean “result” as whether the value of each parameter for every specific page contains special characters when training in the file “db3.json” with path+parameter as the key. Check whether the value of each parameter in the URL or body for every specific page contains special character and compare it to the “result”. If “yes”, We return a 403 page with text “Firewall: Detect an Anomaly. drop requests with parameters containing character from sets not seen during training”.
For example, if “www.facebook.com” has a parameter “username” and it contains special characters, and value of the key “www.facebook.com_username” in the file “db3.json” shows this parameter does not contain special character. We return a 403 page with text “Firewall: Detect an Anomaly. drop requests with parameters containing character from sets not seen during training”.









Other Important Things
More signatures
We add one more signature about checking whether the URL contains “EXTRACTVALUE”, to detect SQL injection syntax.
Test on Joomla 
Before using Web Firewall Application, if we have such a link: 
https://xuwwu.joomla.com/index.php?option=com_jimtawl&view=user&task=user.edit&id=1
 
We get no error.
If we add SQL syntax “union all select 1,2,3/*” at the end of URL, we still get no error.
 
 

After using Web Firewall Application, it shows “Firewall: Detect an Anomaly”

 

Which member of the team did what
Signitures: Xiyun Xie
Training Phase: Wei Cai, Xu Wu
Online Phase: Chongxuan Yin
Report: do together







References
1. https://docs.mitmproxy.org/stable/addons-overview/
2. https://www.cnblogs.com/grandlulu/p/9525417.html (A Chinese website about simple mitmproxy instructions)


