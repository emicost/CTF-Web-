# CTF-Web
#

##CFT.ae 

##https://0xl4ugh.ctf.ae



##Challenge Description

This is an easy challenge, except... it's written in Clojure. Can you find your way through all of these parentheses and come out victorious? - @aelmo



##Introduction 



The challenge was in the web category and involved identifying vulnerabilities in the web application. Initially, I thought the vulnerability might be a classic one, specifically an SQL Injection, since there was a login page available. I decided to try an SQL injection first, as the method is quite simple and effective. Using tools like sqlmap, I attempted to identify the database, find the users' table, and extract login credentials.


This was my initial approach, but unfortunately, it didn’t work. I couldn’t locate the database using sqlmap. I hit a dead end, but luckily, after a few hours, I managed to join a team of students from India, who welcomed me with open arms. After joining their team (which consisted of only three people), we started reevaluating our strategy.

We noticed that the traffic between the client and the server was transmitted via HTTP, which hinted at other possible vulnerabilities. After several attempts, we concluded that the application could be exploited using two vulnerabilities: Mass Assignment and Arbitrary Code Execution






##First Step




In the app's source code,we noticed that it was automatically updating session variables based on the parameters in the URL, without any input validation. This is a classic Mass Assignment vulnerability.

Vulnerabily cod:
```(assoc :session (merge {"prefer" "light"} session query-params)```

This line allows combining parameters from the session with those received via the URL. For example, preference settings can be changed using:

/?prefer=dark&redirect=/

We exploited this behavior by sending additional parameters:


/?username=admin&redirect=/gists

With this request, the application updated the session variable username to the value admin and redirected the user to /gists, granting unauthorized access.






##Second Step




Although I now had access to the /gists path, the flag was not directly available. An analysis of the source code revealed a serious vulnerability in a POST endpoint used for sending gists.


```(defn insert-gist [input]```

 ``` (let [parsed-input (read-string input)]```
 
  ```  ;; Salvare input prelucrat```
  
  ```  (save-to-db parsed-input)))```


The read-string function processes user input without validation. The Clojure documentation warns that this function can be used to execute arbitrary code.

The paiload. 


#=(eval (list (resolve 'println) (System/getenv "FLAG")))

##Rezult 

1.Mass Assignment: Lack of input validation allowed insecure update of session variables, providing unauthorized access.

2.Arbitrary Code Execution: Using the read-string function allowed running a custom payload to access environment variables.


##Conlusion 

This challenge highlights the importance of rigorously validating user input and avoiding unsafe functions such as read-string. By implementing security measures such as:

a.Whitelists for allowed inputs,

b.Strict validation of URL parameters,

c.Avoiding functions that may evaluate unsafe code.

Such vulnerabilities can be effectively prevented

