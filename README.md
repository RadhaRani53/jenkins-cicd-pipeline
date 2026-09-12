# Jenkins CI/CD Pipeline on AWS EC2

Automated CI/CD pipeline that builds a Java Maven application and deploys it to Apache Tomcat on AWS EC2, triggered automatically via GitHub webhooks on every push to master.

---

## Architecture

```
Developer pushes code to GitHub (master branch)
                ↓
        GitHub Webhook fires
                ↓
      Jenkins on EC2 (Port 8080)
                ↓
    Stage 1: Checkout — pulls latest code
                ↓
    Stage 2: Build — mvn clean package
                ↓
    Stage 3: Deploy — copies .jar/.war to Tomcat
                ↓
      App live on Tomcat (Port 8081)
```

---

## Tech Stack

| Tool | Purpose |
|---|---|
| AWS EC2 (Ubuntu) | Cloud server hosting Jenkins + Tomcat |
| Jenkins | CI/CD automation server |
| GitHub + Webhooks | Source control + automatic build trigger |
| Maven | Build tool — compiles and packages the Java app |
| Apache Tomcat 9 | Application server for deployment |
| Java (simple-java-maven-app) | Sample application used for the pipeline |

---

## Pipeline Stages

**Stage 1 — Checkout**
Jenkins pulls the latest code from the `master` branch of the GitHub repository.

**Stage 2 — Build**
Maven compiles the code and packages it (`mvn clean package -DskipTests`). Tests are skipped to keep the pipeline fast for demo purposes.

**Stage 3 — Deploy**
The compiled `.jar` or `.war` artifact is copied to Tomcat's `webapps/` directory. The `|| true` ensures the pipeline doesn't fail if one file type doesn't exist.

---

## Setup

### EC2 Instance
- Ubuntu t2.micro on AWS
- Security Group inbound rules:
  - Port 22 — SSH
  - Port 8080 — Jenkins
  - Port 8081 — Tomcat

### Jenkins Setup
```bash
sudo apt update
sudo apt install -y openjdk-17-jdk jenkins maven git
sudo systemctl start jenkins
# Access at http://EC2-IP:8080
```

### Tomcat Setup
```bash
cd /opt
sudo wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.85/bin/apache-tomcat-9.0.85.tar.gz
sudo tar -xvf apache-tomcat-9.0.85.tar.gz
sudo mv apache-tomcat-9.0.85 tomcat9
sudo /opt/tomcat9/bin/startup.sh
# Access at http://EC2-IP:8081
```

### GitHub Webhook
- GitHub Repo → Settings → Webhooks → Add webhook
- Payload URL: `http://YOUR-EC2-IP:8080/github-webhook/`
- Content type: `application/json`
- Event: Just the push event

---

## Screenshots

### Jenkins Pipeline View
![Pipeline](screenshots/jenkins-pipeline.png)

### Successful Build Console Output
![Build Success](screenshots/build-success.png)

### Tomcat Running
![Tomcat](screenshots/tomcat-running.png)

---

## Source App

This pipeline builds the official Jenkins sample app:  
https://github.com/jenkins-docs/simple-java-maven-app

Forked to: https://github.com/RadhaRani53/simple-java-maven-app

---

## Key Learnings

- Installed and configured Jenkins on AWS EC2 from scratch
- Connected GitHub repo to Jenkins using webhooks for push-triggered builds
- Wrote a 3-stage Declarative Jenkinsfile (Checkout → Build → Deploy)
- Used `mvn clean package` to compile and package a Java Maven app
- Deployed build artifacts to Apache Tomcat automatically on each push

---

## Author

**Radha Rani Adepu** · B.Tech CSE (Data Science), HITAM Hyderabad  
[LinkedIn](https://linkedin.com/in/radharaniadepu) · [GitHub](https://github.com/RadhaRani53)
