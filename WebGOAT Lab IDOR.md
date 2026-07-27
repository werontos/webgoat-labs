Lauch WebGoat

`sudo docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat`

## IDOR (Insecure Direct Object References)
We will use burp suite same as previous one, so open a browser, hit a http://localhost:8080/WebGoat and start from the second challenge IDOR
<img width="979" height="587" alt="image" src="https://github.com/user-attachments/assets/779042fc-d27a-433b-bc66-fd1406135d6e" />

lets use our creds tom:cat to authenticate ourself
<img width="801" height="527" alt="image" src="https://github.com/user-attachments/assets/31a56538-2672-4c57-a242-f7f3ed1e8e70" />

<img width="1659" height="612" alt="image" src="https://github.com/user-attachments/assets/701d0b09-af42-43d4-b976-8aa302084ea9" />

We will analyze the difference between the raw server response and the information displayed in the user profile, compare the data returned by the server with the information rendered on the page to identify hidden attributes. 
We will determine and list the two attributes that are present in the server response but are not visible in the profile
<img width="1686" height="854" alt="image" src="https://github.com/user-attachments/assets/904de0ae-60b8-457d-a752-000e1307d2aa" />

I clicked on a View Profile button and there is a dropdown with the info Name,Color,Size, but lets analyze server response
<img width="1483" height="757" alt="image" src="https://github.com/user-attachments/assets/9114add6-2d34-43df-8676-444fac11e3b1" />

As we can see, there is more given info than on site, so lets fill the field with role and userId
<img width="1137" height="719" alt="image" src="https://github.com/user-attachments/assets/149a82aa-1681-4de5-8fa4-4b893a7b3fa6" />

Correct, move further
<img width="1664" height="748" alt="image" src="https://github.com/user-attachments/assets/973518b0-f870-42e8-933f-b13185c0258e" />

So, here we need to enter the url path to our profile, but we dont know our user profile id, so lets analyze the response + request
<img width="1038" height="580" alt="image" src="https://github.com/user-attachments/assets/a3a22c13-bc38-4096-9eb2-38866a08be54" />

as we can see, our path is /WebGoat/IDOR/profile, and our Tom Cat userId is 2342384
So, our answer for this one will be WebGoat/IDOR/profile/2342384
<img width="1636" height="709" alt="image" src="https://github.com/user-attachments/assets/c452f343-69be-4f52-b12e-2fde7b195060" />

So, lets move further
<img width="1644" height="716" alt="image" src="https://github.com/user-attachments/assets/41d9e1c1-4444-4513-a743-17e47f10d6b7" />

1 task is to view someone else profile, i think its easy
<img width="1251" height="648" alt="image" src="https://github.com/user-attachments/assets/91bf012a-55b0-4671-8174-49819c97994f" />

It literally says us to brute userId's as we can see in the request path
Lets brute some id's to see what we can get
Forward our request to intruder
<img width="983" height="532" alt="image" src="https://github.com/user-attachments/assets/2d88ee3d-2056-47bf-8fc2-6dc1ab39107f" />

<img width="1713" height="629" alt="image" src="https://github.com/user-attachments/assets/f619fc2d-fc58-451f-9a22-eafbffdb2252" />

Set our payload to Numbers and lets start from 1 to 99, small brute force.
<img width="1710" height="667" alt="image" src="https://github.com/user-attachments/assets/bba3afb3-35b9-4d1f-9776-47a07641953b" />

hit Start attack
<img width="1185" height="838" alt="image" src="https://github.com/user-attachments/assets/e1d93804-6ce3-434c-9897-df719ef743fd" />

Success, we got an account Buffalo Bill on 88's request
First task complete
Now we need to edit profile
<img width="1343" height="216" alt="image" src="https://github.com/user-attachments/assets/e0c937c1-ddf5-4d7e-9251-00c2c63f3db8" />

So, we now that to change smth we use PUT request, lets immediately change it
<img width="1341" height="494" alt="image" src="https://github.com/user-attachments/assets/7ca1fbb3-785e-4ac5-a438-fa3fd2306dee" />

And now take a look at the Content-Type, Server accepts json, so we will use json formatted request to edit user, we already have userid in path
And as we can see, Well Done we modified someone else profile using IDOR Vulnerability
<img width="1431" height="688" alt="image" src="https://github.com/user-attachments/assets/2555e455-83e8-4ba3-8066-8542902b3971" />

