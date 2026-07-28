# WebGoat Lab — Missing Function Level Access Control

## Launch WebGoat

```bash
sudo docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat
```

## Objective

Locate hidden admin functionality relying on obscurity rather than proper authorization, then escalate privileges to reach it.

## Walkthrough

### Finding the Hidden Menu

This challenge opens with a classic case of **relying on obscurity**. The task was to find hidden dropdown items on the page, so I started digging through the menu.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/b94cee46-e213-42fe-a7cf-6e6bbd2eefe0" />

There it is — a hidden dropdown.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/ab7d09b5-843d-4b71-8052-4a15959b0398" />

Expanded all the hidden menu items:

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/edf5e4f2-4447-48c2-9aad-1c32e854f824" />

The dropdown label is **Admin**, with two options underneath it:

- **Users**
- **Config**

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/7dcaa6ae-2969-4320-af5e-7b41a5f143e2" />

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/adee70e8-9283-481f-b5bd-02257c7a2f09" />

### Gathering User Info

Next task: gather user information. This one took me almost an hour, but I got there in the end.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/4bf03502-9389-4224-91ad-540ca476e742" />

Fired up Burp Suite and hit the WebGoat page. Here's where it gets interesting.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/30e1642c-c6dd-412a-8b8e-bdc8c1b7dd19" />

Spotted an endpoint: `access-control/users-admin-fix`. Hitting it directly:

```text
http://localhost:8080/WebGoat/access-control/users-admin-fix
```

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/142dd385-0cda-48b6-b82b-eee851c9a20a" />

That returned a **415** response. Digging further and hitting:

```text
http://localhost:8080/WebGoat/access-control/users
```

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/d34af6d2-6e77-4fe3-91ca-97630b871757" />

This one threw a **500 Internal Server Error**, meaning the server hit an unexpected condition it couldn't handle. Time to dig into the request in Burp.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/290a7c71-163d-4114-a729-e15bdd5dc07c" />

The lab's hint pointed me in the right direction:

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/3a51a740-265f-447e-ae95-bfaf29a54d4d" />

Simply adding a proper **`Content-Type`** header to the request and hitting **Send** fixed it.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/b1f4601f-3a4f-47ae-ac87-fdf0403d826c" />

The response came back **200**, with a set of hashes. Grabbed the hash and pasted it into the field.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/b64eef10-55a4-4ece-8fbc-114ff4144370" />

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/c510db0c-3cd8-4b0b-b4a6-5530e0c5036b" />

### Escalating to Admin

Final step: escalate the account to admin and see what's hiding behind `/access-control/users-admin-fix`. The hint made the path clear:

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/cb407fae-4549-46d1-ae00-d38d62d0cb08" />

Modified the request the same way and obtained admin privileges.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/250b160f-fabb-4103-be27-df0d5b40216c" />

With admin access in hand, modified the request again to pull the hashes:

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/1321f099-7c8f-45e9-b6b7-0eec7680a318" />

## Result

Retrieved the final hash:

```text
d4T2ahJN4fWP83s9JdLISio7Auh4mWhFT1Q38S6OewM=
```

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/17c77aa1-2c2e-49ae-8035-1aecb68f61f6" />

## Takeaway

Hiding admin functionality through UI obscurity (removing menu items, unlisted endpoints) is not access control. Every sensitive endpoint needs a server-side authorization check tied to the user's role, independent of whether the client-side interface exposes it.
