
========================================================================================================================

                 DEVOPS PROJECT #5
========================================================================================================================

PROJECT TITLE:

AWS High Availability Web Application
ALB + Auto Scaling + Multi-AZ + Private EC2

============================================================
3. PROJECT #5 SOLUTION
============================================================

Project #5 mein:

                    INTERNET
                        |
                        v
               INTERNET GATEWAY
                        |
                        v
              APPLICATION LOAD
                 BALANCER
                    ALB
                        |
             +----------+----------+
             |                     |
             v                     v
        PRIVATE AZ-1          PRIVATE AZ-2
             |                     |
             v                     v
        EC2 / TOMCAT           EC2 / TOMCAT
             |                     |
             +----------+----------+
                        |
                        v
                   APPLICATION
============================================================
NGINX REVERSE PROXY USING DEFAULT CONFIGURATION
============================================================

============================================================
1. UPDATE UBUNTU
============================================================

sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
sudo systemctl status nginx
sudo systemctl start nginx
sudo systemctl enable nginx


============================================================
4. CHECK DEFAULT NGINX CONFIGURATION
============================================================

Default configuration file:

/etc/nginx/sites-available/default


Check the file:

sudo nano /etc/nginx/sites-available/default


============================================================
5. REMOVE DEFAULT WEB PAGE
============================================================

The default HTML page is:

/var/www/html/index.nginx-debian.html


Remove it:

sudo rm /var/www/html/index.nginx-debian.html


============================================================
6. CREATE YOUR OWN WEB PAGE
============================================================

sudo nano /var/www/html/index.html


Add:

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="theme-color" content="#0b1220">
  <title>Devops | AWS High Availability</title>
  <style>
    :root {
      --bg: #0b1220;
      --panel: #121d31;
      --panel2: #17253d;
      --text: #edf4ff;
      --muted: #a7b5ca;
      --border: #2b3b56;
      --blue: #56c8ff;
      --green: #4ade80;
      --orange: #ffb454;
    }
    * { box-sizing: border-box; }
    body {
      margin: 0;
      color: var(--text);
      background: radial-gradient(circle at 15% 0%, #182c4a 0, var(--bg) 42%);
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Arial, sans-serif;
      line-height: 1.6;
    }
    .wrap { width: min(1120px, calc(100% - 36px)); margin: auto; }
    header {
      display: flex; align-items: center; justify-content: space-between;
      gap: 16px; padding: 22px 0; border-bottom: 1px solid var(--border);
    }
    .brand { font-weight: 800; font-size: 1.15rem; letter-spacing: .03em; }
    .brand span { color: var(--blue); }
    .pill {
      border: 1px solid #23684a; background: #123a2c; color: #a7f3c2;
      border-radius: 999px; padding: 6px 12px; font-size: .8rem; white-space: nowrap;
    }
    .hero { text-align: center; padding: 76px 0 48px; }
    .eyebrow {
      display: inline-block; border: 1px solid #285b85; background: #102b45;
      color: #a9e3ff; padding: 7px 13px; border-radius: 999px;
      font-size: .78rem; font-weight: 700; letter-spacing: .1em;
    }
    h1 { font-size: clamp(2.25rem, 6vw, 4.4rem); line-height: 1.08; margin: 22px 0 18px; }
    h1 span { color: var(--blue); }
    .intro { max-width: 760px; margin: auto; color: var(--muted); font-size: 1.08rem; }
    .actions { display: flex; justify-content: center; flex-wrap: wrap; gap: 12px; margin-top: 28px; }
    .btn {
      border: 1px solid var(--border); border-radius: 10px; padding: 11px 17px;
      color: var(--text); background: var(--panel2); text-decoration: none; font-weight: 650;
    }
    .btn.primary { background: var(--blue); color: #06111d; border-color: var(--blue); }
    section { padding: 26px 0; }
    .section-head { text-align: center; margin-bottom: 25px; }
    h2 { margin: 0 0 8px; font-size: clamp(1.5rem, 3vw, 2rem); }
    .section-head p, .muted { color: var(--muted); margin: 0; }
    .architecture {
      display: grid; grid-template-columns: 1fr auto 1fr auto 1fr;
      align-items: stretch; gap: 14px;
    }
    .node {
      background: linear-gradient(160deg, var(--panel2), var(--panel));
      border: 1px solid var(--border); border-radius: 16px; padding: 23px 18px;
      min-width: 0;
    }
    .node .icon { font-size: 2rem; margin-bottom: 8px; }
    .node h3 { margin: 0 0 7px; font-size: 1.08rem; }
    .node p { color: var(--muted); font-size: .92rem; margin: 0; }
    .tag { display: inline-block; margin-top: 13px; padding: 4px 9px; border-radius: 7px; font-size: .75rem; font-weight: 700; }
    .tag.blue { color: #a9e3ff; background: #123451; }
    .tag.green { color: #a7f3c2; background: #123a2c; }
    .tag.orange { color: #ffe0b0; background: #4a2e14; }
    .arrow { display: grid; place-items: center; color: var(--orange); font-size: 1.8rem; }
    .grid3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
    .info {
      background: rgba(18, 29, 49, .85); border: 1px solid var(--border);
      border-radius: 14px; padding: 22px;
    }
    .info h3 { margin: 0 0 8px; color: var(--blue); }
    .info p { margin: 0; color: var(--muted); font-size: .94rem; }
    .flow {
      border: 1px solid var(--border); border-radius: 16px; padding: 24px;
      background: rgba(18, 29, 49, .72); text-align: center;
    }
    .flowline { color: var(--muted); font-weight: 700; word-spacing: 3px; }
    .flowline strong { color: var(--blue); }
    .notice {
      border: 1px solid #285b85; background: #10243a; color: #c8eaff;
      border-radius: 12px; padding: 17px 19px; margin: 18px 0 42px;
    }
    footer { border-top: 1px solid var(--border); padding: 24px 0 30px; color: var(--muted); text-align: center; font-size: .88rem; }
    footer strong { color: var(--blue); }
    @media (max-width: 850px) {
      .architecture { grid-template-columns: 1fr; }
      .arrow { transform: rotate(90deg); min-height: 24px; }
      .grid3 { grid-template-columns: 1fr; }
      .hero { padding-top: 55px; }
    }
    @media (max-width: 520px) {
      .wrap { width: min(100% - 24px, 1120px); }
      header { align-items: flex-start; flex-direction: column; }
      .hero { padding-bottom: 28px; }
    }
  </style>
</head>
<body>
  <header class="wrap">
    <div class="brand">AWS and <span>DevOps</span></div>
    <div class="pill">● AWS HA Project · Demo</div>
  </header>

  <main class="wrap">
    <section class="hero">
      <div class="eyebrow">PROJECT #5 · HIGH AVAILABILITY</div>
      <h1>AWS High Availability<br><span>Web Application</span></h1>
      <p class="intro">
        A resilient two-AZ web application using an Application Load Balancer,
        Target Group, Auto Scaling Group, private EC2 instances, Nginx reverse proxy,
        and Apache Tomcat.
      </p>
      <div class="actions">
        <a class="btn primary" href="#architecture">Explore Architecture</a>
        <a class="btn" href="#request-flow">View Request Flow</a>
      </div>
    </section>

    <section id="architecture">
      <div class="section-head">
        <h2>Application Architecture</h2>
        <p>Public entry point, private application servers, and health-based routing.</p>
      </div>
      <div class="architecture">
        <article class="node">
          <div class="icon">🌐</div>
          <h3>Application Load Balancer</h3>
          <p>Receives client traffic through its public listener and forwards requests to healthy registered targets.</p>
          <span class="tag orange">Public subnets · HTTP/HTTPS</span>
        </article>
        <div class="arrow" aria-hidden="true">→</div>
        <article class="node">
          <div class="icon">⚖️</div>
          <h3>Target Group</h3>
          <p>Performs health checks and sends traffic only to healthy targets registered by the Auto Scaling Group.</p>
          <span class="tag blue">Health check: /</span>
        </article>
        <div class="arrow" aria-hidden="true">→</div>
        <article class="node">
          <div class="icon">🔒</div>
          <h3>Private EC2 · Multi-AZ</h3>
          <p>Two private application instances run Nginx on port 80 and Tomcat on port 8080.</p>
          <span class="tag green">AZ-1 + AZ-2 · No public IP</span>
        </article>
      </div>
    </section>

    <section>
      <div class="section-head">
        <h2>What makes this highly available?</h2>
        <p>Core AWS components used in this project.</p>
      </div>
      <div class="grid3">
        <article class="info">
          <h3>🩺 Health Checks</h3>
          <p>The ALB checks registered targets. Unhealthy targets stop receiving new requests until healthy again.</p>
        </article>
        <article class="info">
          <h3>📈 Auto Scaling</h3>
          <p>The Auto Scaling Group maintains the configured capacity and can replace failed EC2 instances.</p>
        </article>
        <article class="info">
          <h3>🛡️ Private Backend</h3>
          <p>Tomcat is not exposed directly to the internet. Security groups restrict backend access to approved sources.</p>
        </article>
      </div>
    </section>

    <section id="request-flow">
      <div class="section-head">
        <h2>Request Flow</h2>
        <p>What happens when a user opens the website?</p>
      </div>
      <div class="flow">
        <p class="flowline">
          User → <strong>ALB</strong> → <strong>Target Group</strong> →
          <strong>Nginx :80</strong> → <strong>Tomcat :8080</strong>
        </p>
        <p class="muted">The response returns through Nginx and the ALB to the user's browser.</p>
      </div>
      <div class="notice">
        <strong>Deployment note:</strong> Deploy this page as the root web page of the same Tomcat application on both private EC2 instances. Configure Nginx on each instance to proxy requests to <code>http://127.0.0.1:8080</code>, and configure the ALB Target Group to send traffic to the instances on port 80.
      </div>
    </section>
  </main>

  <footer>
    <div class="wrap">© 2026 <strong>AWS and DevOps</strong> · Project #5 · AWS High Availability Web Application</div>
  </footer>
</body>
</html>



============================================================
7. CREATE SYMBOLIC LINK FOR DEFAULT CONFIGURATION
============================================================

Normally Ubuntu already creates this link during Nginx installation.

Check:

ls -l /etc/nginx/sites-enabled/


Expected:

default -> /etc/nginx/sites-available/default


If the symbolic link does not exist:

sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/default



============================================================
9. EDIT DEFAULT NGINX CONFIGURATION
============================================================

sudo nano /etc/nginx/sites-available/default


Use the following configuration:

server {

    listen 80 default_server;
    listen [::]:80 default_server;

    server_name _;

    location / {

        proxy_pass http://127.0.0.1:8080;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

    }
}


============================================================
10. UNDERSTAND THE REVERSE PROXY
============================================================

The important line is:

proxy_pass http://127.0.0.1:8080;


It means:

Browser
   |
   | Port 80
   ↓
Nginx
   |
   | proxy_pass
   ↓
127.0.0.1:8080
   |
   ↓
Tomcat





