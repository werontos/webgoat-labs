Lauch WebGoat

`sudo docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat`

## A1 Hijack a session
Lets start from opening Burp Suite
<img width="739" height="532" alt="image" src="https://github.com/user-attachments/assets/f3e3356c-0e36-4c54-95c6-d97d4b079e3f" />

We will use built in chromium browser to analyze all the requests that coming through it.
<img width="1113" height="655" alt="image" src="https://github.com/user-attachments/assets/6cbb6e9c-ef01-42b0-bbb4-6a71a4fb63d1" />

<img width="483" height="489" alt="image" src="https://github.com/user-attachments/assets/ce9bf300-1cdd-4e7b-85c4-2cfacb3c1a46" />

Once browser is opened hit http://localhost:8080/WebGoat to enter the site
Then simply register new account with whatever creds you want
<img width="1710" height="836" alt="image" src="https://github.com/user-attachments/assets/813016bd-f4e9-4657-9d64-79bfb128bd08" />

So we're trying to predict the `hijack_cookie` value. It is used to differentiate authenticated and anonymous users
Then hit and hop on burp
<img width="1078" height="546" alt="image" src="https://github.com/user-attachments/assets/700d17cf-7095-4db4-af58-355c216e19f1" />

As we can see, we already have hijack_cookie value in our request
<img width="1555" height="746" alt="image" src="https://github.com/user-attachments/assets/bca7ffdf-c945-4172-b629-c2a2e6a34026" />

What we gonna do is simply forward this request to repeater
<img width="829" height="735" alt="image" src="https://github.com/user-attachments/assets/e0721b45-5de2-4e20-9eb0-b201398fd7f3" />

Then clear the `hijack_value` and hit send
<img width="1245" height="680" alt="image" src="https://github.com/user-attachments/assets/dd55ed22-4f17-42a0-9f56-2d52657bb125" />

as we can see, we got a `hijack_cookie` from the server response
The next step is we will generate 5-10 hijack_cookies to understand their randomness
What i'll do is just enter them in .txt file to compare them later
<img width="895" height="513" alt="image" src="https://github.com/user-attachments/assets/bd5f78a6-f266-4b64-a2e2-962afc14e212" />

So the logic behind this is simple, i can assume that the first part is a user session, second part is probably a timestamp, as we can see, we probably got a session between 52-54, so 53 probably someone's session
290320782792482155==2==-1785098548804
290320782792482155==4==-1785098566256
So, we'll do a simple brute force of 2 last digits with the hope to get a session
Copy the hijack_cookie value into your cookie and forward the request to intruder
<img width="1165" height="689" alt="image" src="https://github.com/user-attachments/assets/108e6cdc-0bc9-4530-9d4b-5f714fe212b6" />

Then copy the previous `hijack_cookie` session that is close to user session(in my case 2903207827924821552-1785098548804) , and change the USER SESSION PART last 2 digits to 53, and then put last 2 digits of timestamp in §, and set payloda type to *Numbers* {range} from 1 to 99
<img width="1694" height="772" alt="image" src="https://github.com/user-attachments/assets/6a52b452-bcc2-4a39-879a-7701b1e41951" />

Hit start attack
<img width="1471" height="835" alt="image" src="https://github.com/user-attachments/assets/225fee54-ec13-461e-a422-d18226d15292" />

As we can see, we successfully hijacked the session using Burp Suite
