# WebGoat Lab — A1: Hijack a Session

## Launch WebGoat

```bash
sudo docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat
```

## Objective

Predict a valid `hijack_cookie` value and use it to hijack another user's authenticated session.

## Walkthrough

Let's start by opening Burp Suite.

<img width="739" height="532" alt="image" src="https://github.com/user-attachments/assets/f3e3356c-0e36-4c54-95c6-d97d4b079e3f" />

I used the built-in Chromium browser to intercept and analyze every request going through it.

<img width="1113" height="655" alt="image" src="https://github.com/user-attachments/assets/6cbb6e9c-ef01-42b0-bbb4-6a71a4fb63d1" />

<img width="483" height="489" alt="image" src="https://github.com/user-attachments/assets/ce9bf300-1cdd-4e7b-85c4-2cfacb3c1a46" />

Once the browser was up, I opened `http://localhost:8080/WebGoat` and registered a new account with arbitrary credentials.

<img width="1710" height="836" alt="image" src="https://github.com/user-attachments/assets/813016bd-f4e9-4657-9d64-79bfb128bd08" />

The goal here is to predict the value of the **`hijack_cookie`**, which the app uses to tell authenticated users apart from anonymous ones. I logged in and switched over to Burp.

<img width="1078" height="546" alt="image" src="https://github.com/user-attachments/assets/700d17cf-7095-4db4-af58-355c216e19f1" />

Sure enough, the `hijack_cookie` value was already sitting in the request.

<img width="1555" height="746" alt="image" src="https://github.com/user-attachments/assets/bca7ffdf-c945-4172-b629-c2a2e6a34026" />

### Forwarding to Repeater

I sent the request to **Repeater**:

<img width="829" height="735" alt="image" src="https://github.com/user-attachments/assets/e0721b45-5de2-4e20-9eb0-b201398fd7f3" />

Then cleared the `hijack_value` and hit **Send**:

<img width="1245" height="680" alt="image" src="https://github.com/user-attachments/assets/dd55ed22-4f17-42a0-9f56-2d52657bb125" />

The server responded with a fresh `hijack_cookie`. Next step was to generate 5–10 more cookies to get a feel for how they're structured and how random they actually are. I logged each one into a `.txt` file for comparison.

<img width="895" height="513" alt="image" src="https://github.com/user-attachments/assets/bd5f78a6-f266-4b64-a2e2-962afc14e212" />

### Spotting the Pattern

The logic turned out to be pretty straightforward. I assumed the cookie was built from two parts: a **user session identifier**, followed by a **timestamp**. Comparing samples, it looked like sessions were being generated somewhere in the **52–54** range, meaning session `53` most likely belonged to another user:

```text
290320782792482155==2==-1785098548804
290320782792482155==4==-1785098566256
```

Based on that, the plan was to brute-force the last two digits of the timestamp while targeting session `53`.

I copied the `hijack_cookie` value into my own cookie header and forwarded the request to **Intruder**:

<img width="1165" height="689" alt="image" src="https://github.com/user-attachments/assets/108e6cdc-0bc9-4530-9d4b-5f714fe212b6" />

### Setting Up the Attack

I took the previous `hijack_cookie` session closest to the target (in my case `2903207827924821552-1785098548804`), changed the **last two digits of the session part** to `53`, and marked the **last two digits of the timestamp** as the payload position (`§...§`). Payload type was set to **Numbers**, ranging from `1` to `99`.

<img width="1694" height="772" alt="image" src="https://github.com/user-attachments/assets/6a52b452-bcc2-4a39-879a-7701b1e41951" />

Hit **Start attack**:

<img width="1471" height="835" alt="image" src="https://github.com/user-attachments/assets/225fee54-ec13-461e-a422-d18226d15292" />

## Result

The brute force succeeded — the target's session was hijacked using Burp Suite's Intruder module.

## Takeaway

Session identifiers built from predictable, low-entropy components (like a sequential session counter plus a coarse timestamp) can be brute-forced in a small keyspace. Session tokens should be generated using a cryptographically secure random source with enough entropy to make brute-forcing computationally infeasible.
