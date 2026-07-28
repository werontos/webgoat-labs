# WebGoat Lab — IDOR (Insecure Direct Object References)

## Launch WebGoat

```bash
sudo docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat
```

## Objective

Exploit an Insecure Direct Object Reference to view and modify another user's profile without proper authorization.

## Walkthrough

I used Burp Suite the same way as in the previous lab. Opened the browser, hit `http://localhost:8080/WebGoat`, and started from the second challenge: **IDOR**.

<img width="739" height="532" alt="image" src="https://github.com/user-attachments/assets/779042fc-d27a-433b-bc66-fd1406135d6e" />

Logged in with the `tom:cat` credentials.

<img width="1113" height="655" alt="image" src="https://github.com/user-attachments/assets/31a56538-2672-4c57-a242-f7f3ed1e8e70" />

<img width="483" height="489" alt="image" src="https://github.com/user-attachments/assets/701d0b09-af42-43d4-b976-8aa302084ea9" />

### Comparing the Raw Response with the Rendered Page

The task was to compare the raw server response against what's actually displayed on the profile page, and identify hidden attributes — fields returned by the server but not rendered in the UI.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/904de0ae-60b8-457d-a752-000e1307d2aa" />

Clicking **View Profile** only shows a dropdown with **Name**, **Color**, and **Size**. But the raw server response tells a different story:

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/9114add6-2d34-43df-8676-444fac11e3b1" />

As expected, the response contains more data than the page displays — specifically **`role`** and **`userId`**.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/149a82aa-1681-4de5-8fa4-4b893a7b3fa6" />

Correct — moving on.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/973518b0-f870-42e8-933f-b13185c0258e" />

### Finding the Profile Path

Next task was to figure out the URL path to my own profile, without knowing my user ID up front. I dug through the request/response pair to find it:

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/a3a22c13-bc38-4096-9eb2-38866a08be54" />

The path turned out to be `/WebGoat/IDOR/profile`, and Tom Cat's `userId` is `2342384`. So the full answer is:

```text
WebGoat/IDOR/profile/2342384
```

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/c452f343-69be-4f52-b12e-2fde7b195060" />

Moving on.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/41d9e1c1-4444-4513-a743-17e47f10d6b7" />

### Viewing Someone Else's Profile

Task 1 was to view another user's profile — figured this would be simple.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/91bf012a-55b0-4671-8174-49819c97994f" />

The lab practically spells it out: brute-force the `userId` values, since it's exposed directly in the request path. Forwarded the request to **Intruder**:

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/2d88ee3d-2056-47bf-8fc2-6dc1ab39107f" />

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/f619fc2d-fc58-451f-9a22-eafbffdb2252" />

Set the payload type to **Numbers**, ranging from `1` to `99` — a small, targeted brute force.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/bba3afb3-35b9-4d1f-9776-47a07641953b" />

Hit **Start attack**:

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/e1d93804-6ce3-434c-9897-df719ef743fd" />

Success — account **Buffalo Bill** turned up on request `88`. First task complete.

### Editing Someone Else's Profile via IDOR

Next step: edit that profile.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/e0c937c1-ddf5-4d7e-9251-00c2c63f3db8" />

Since modifying a resource means a `PUT` request, I switched to `PUT` right away.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/7ca1fbb3-785e-4ac5-a438-fa3fd2306dee" />

Checking the **`Content-Type`** header showed the server accepts JSON, so I sent a JSON-formatted body to edit the user — the `userId` was already in the path.

**Result:** the profile of another user was successfully modified using the IDOR vulnerability.

<img width="1414" height="842" alt="image" src="https://github.com/user-attachments/assets/2555e455-83e8-4ba3-8066-8542902b3971" />
