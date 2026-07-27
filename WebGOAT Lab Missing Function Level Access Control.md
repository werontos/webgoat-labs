# Lauch WebGoat

`sudo docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat`

## Missing Function Level Access Control

<img width="1341" height="681" alt="image" src="https://github.com/user-attachments/assets/b94cee46-e213-42fe-a7cf-6e6bbd2eefe0" />

Here we got a Relying on obscurity
Our task is to find a hidden dropdown items on a page, lets analyze the dropdown menu
<img width="767" height="627" alt="image" src="https://github.com/user-attachments/assets/ab7d09b5-843d-4b71-8052-4a15959b0398" />

Here we can see a hidden dropdown
<img width="1031" height="545" alt="image" src="https://github.com/user-attachments/assets/edf5e4f2-4447-48c2-9aad-1c32e854f824" />
Expand all hidden-menu-items
<img width="925" height="404" alt="image" src="https://github.com/user-attachments/assets/7dcaa6ae-2969-4320-af5e-7b41a5f143e2" />

And we can see that the Label name of the dropdown is Admin
And here is 2 options 
Users
Config
<img width="1016" height="702" alt="image" src="https://github.com/user-attachments/assets/adee70e8-9283-481f-b5bd-02257c7a2f09" />

So, lets move to the next task
<img width="1366" height="553" alt="image" src="https://github.com/user-attachments/assets/4bf03502-9389-4224-91ad-540ca476e742" />

And here we need to gather users info, i almost an hour bout i found the solution
Lets launch the Burp Suite
And hit the WebGoat page
So, now pay an attention
<img width="925" height="404" alt="image" src="https://github.com/user-attachments/assets/30e1642c-c6dd-412a-8b8e-bdc8c1b7dd19" />

Here we have a access-control/users-admin-fix
Lets hit it http://localhost:8080/WebGoat/access-control/users-admin-fix
<img width="1052" height="693" alt="image" src="https://github.com/user-attachments/assets/142dd385-0cda-48b6-b82b-eee851c9a20a" />

we are hitting 415 response, but lets dig and hit http://localhost:8080/WebGoat/access-control/users
<img width="1387" height="753" alt="image" src="https://github.com/user-attachments/assets/d34af6d2-6e77-4fe3-91ca-97630b871757" />

As we can see, server 500 Internal Server Error meaning the server encountered an unexpected condition that prevented it from fulfilling the request.
Lets analyze the request in Burp Suite
<img width="1264" height="673" alt="image" src="https://github.com/user-attachments/assets/290a7c71-163d-4114-a729-e15bdd5dc07c" />

lets take a look at the hint we got
<img width="790" height="137" alt="image" src="https://github.com/user-attachments/assets/3a51a740-265f-447e-ae95-bfaf29a54d4d" />

so, just include Content-Type into the request and hit send
<img width="1357" height="656" alt="image" src="https://github.com/user-attachments/assets/b1f4601f-3a4f-47ae-ac87-fdf0403d826c" />

as you can see, our response gives us 200 and hashes, grab the hash and paste into the field
<img width="910" height="456" alt="image" src="https://github.com/user-attachments/assets/b64eef10-55a4-4ece-8fbc-114ff4144370" />

<img width="1273" height="394" alt="image" src="https://github.com/user-attachments/assets/c510db0c-3cd8-4b0b-b4a6-5530e0c5036b" />

now we need to escalate our account to admin and see whats inside of /access-control/users-admin-fix
As we can see on hint
<img width="1419" height="219" alt="image" src="https://github.com/user-attachments/assets/cb407fae-4549-46d1-ae00-d38d62d0cb08" />

we need to modify the request same as this and obtain admin priviligues8
<img width="1352" height="782" alt="image" src="https://github.com/user-attachments/assets/250b160f-fabb-4103-be27-df0d5b40216c" />

Once we get it
Modify request to get hashes like this
<img width="1208" height="696" alt="image" src="https://github.com/user-attachments/assets/1321f099-7c8f-45e9-b6b7-0eec7680a318" />

And we get our hashes
d4T2ahJN4fWP83s9JdLISio7Auh4mWhFT1Q38S6OewM=
<img width="990" height="690" alt="image" src="https://github.com/user-attachments/assets/17c77aa1-2c2e-49ae-8035-1aecb68f61f6" />

