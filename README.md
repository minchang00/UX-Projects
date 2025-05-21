# Onboarding Application Pipeline

## Overview
This is an onboarding user-guide for the pipeline, explaining the step-by-step process of setting up a pipeline that will help deliver applications such as, OCIO's enterprise GitHub organizations (bcgov and bcgov-c), NR Broker, and CI/CD to production. 

### Prerequisites
- Team name
  - All team members need to first access NR Broker UI to get member IDIR available.    
**<sub> This step is important as it gives team members access to services that are required to run the systems properly.</sub>**
  - At least add one owner to manage all team members
  - Make sure the Team name is unique, can't have duplicates
- Project Name
- Service Name
- Tools Secret (eg. secrets for Broker Token & Jenkins Tokens)
- Software or Framework Name (eg. Java, Nodejs, etc)
- GitHub Repository for application
  - All the the details mentioned above should be included

**After completing the prerequisite, please inform your PO to inform the OSCAR Team about the completed set-up.**
<br>
<br>
## 1. Broker Setup & Generate Polaris Jekins Pipeline - (OSCAR Team's Task)

### a. Broker Setup for Application - [NR Broker - New Service](https://apps.nrs.gov.bc.ca/int/confluence/display/OSCAR/Enable+a+new+app+to+use+the+NR+Broker?)

- Create a team on NR Broker UI: https://apps.nrs.gov.bc.ca/int/confluence/x/DJlZDQ (permission required for access)
- Individual broker account, project/service setup: https://apps.nrs.gov.bc.ca/int/confluence/x/XZTZCw
- Configure source repo for the service and add GitHub repo URL
- Add the service to the Github App (bcgov-nr/broker-jwt-update) and grant read access to be able to sync on the GitHub Application Repository
- Generate Broker JWT in Broker and sync to GitHub Repo as a secret
- Test the deployment to delivery

**Once the deployment is successful, the OSCAR team will inform the Devs the Broker set up is done.**
<br>

### b. Generate Polaris Jenkins Pipeline - [Polaris Pipeline](https://apps.nrs.gov.bc.ca/int/confluence/display/OSCAR/Polaris+Pipelines)

***If the deployment was successful from the Broker Setup, the nr-repository-composer generator would have already been ran by the OSCAR team.***

- Check the Usage section(https://github.com/bcgov/nr-repository-composer/blob/main/README.md) to run this nr-repository-composer generators on the checkout app repository.
- After run, it creates or updates the files for building and deploying GitHub Action jobs in the working directory.
- Update the files to the GitHub application Repository
<br>

## 2. Polaris Jenkins Job - (Developer's Task)

- Create Jenkins Deployment job for the application/service https://cd.io.nrs.gov.bc.ca
- Examples:
  
    Maven build deployment Job: https://cd.io.nrs.gov.bc.ca/job/oneteam-example/job/java-maven-pipeline-example/

    Nodejs package deployment job:https://cd.io.nrs.gov.bc.ca/job/nodejs-sample/job/nodejs-sample/

    Jasper Report job: https://cd.io.nrs.gov.bc.ca/job/fnirs/job/fnirs-jasper-reports/
- **Add explanation on how to run their deployments:**
  - **How to run GitHub Actions**
  - **Explanation for the purpose of each action**
- Add Jenkins job token to Vault tools path as JENKINS_TOKEN and sync secrets to GitHub repo
  - Use https://randomkeygen.com/ to generate a random key for JENKINS_TOKEN
  - Copy this key to Jenkins Job field: Trigger builds remotely → Authentication Token
  - Copy this key to Github Repo secrets as value for JENKINS_TOKEN
  - Copy this key to Vault tools path as JENKINS_TOKEN for Broker Sync process
<br>

Overall GitHub workflows: https://apps.nrs.gov.bc.ca/int/confluence/x/cIrZCw

