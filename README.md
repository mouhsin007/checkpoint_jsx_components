<div id="top"></div>

<!-- PROJECT LOGO -->
<br />
<div align="center">
 
 ![Logo][logo-screenshot]
 
<h3 align="center">Claims Tool Project</h3>  
  <p align="center">        
    <a href="https://confluence.axa.com/confluence/display/AGSSPA2/Claims"><strong>Explore the docs »</strong></a>
    <br />
    <br />
    <a href="http://dasah013.ppprivmgmt.intraxa:8002/request-search">View Demo</a>
    ·
    <a href="https://jira.axa.com/jira/secure/RapidBoard.jspa?rapidView=7443&projectKey=CLAIM">Report Bug</a>    
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>      
    </li>
    <li>
      <a href="#software-installation">Software Installation</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#cloning-the-repositories">Cloning the repositories</a></li>
        <li><a href="#creating-the-databases">Creating the databases</a></li>
      </ul>
    </li>
    <li>
      <a href="#software-configuration">Software Configuration</a>
      <ul>
        <li><a href="#setting-up-configuration-files">Setting up configuration files</a></li>        
        <li><a href="#installing-the-packages">Installing the packages</a></li>                
      </ul>      
    </li>
    <li>
      <a href="#launching-the-application">Launching the application</a>      
    </li>            
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>    
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

[![Product Name Screen Shot][product-screenshot]](http://dasah013.ppprivmgmt.intraxa:8002/request-search)
Claims is a Digital Tool for Reinsurance Business. It allows the users to:
    <ul>
      <li>Receive new Claim Requests via Email</li>
      <li>Manage the Claim Request Information</li>
      <li>Maintain conversations with the Claim Request Responsibles</li>
      <li>Generate the Loss Approval to manage the insurance info of the Claim Request</li>
      <li>Generate the Loss Advices that will be sent to the Reinsurers</li>
      <li>Etc</li>
  </ul>
<p align="right">(<a href="#top">back to top</a>)</p>



### Built With

* [Node.js](https://nodejs.org/)
* [Angular](https://angular.io/)
* [PrimeNg](https://www.primefaces.org/primeng/#/)
* [.NET Framework](https://dotnet.microsoft.com/en-us/learn)
* [Bootstrap](https://getbootstrap.com)
* [Typescript](https://www.typescriptlang.org/)

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- GETTING STARTED -->
## Getting Started

In order to launch the application locally in a DEV environment, we need to understand the <a href="https://confluence.axa.com/confluence/display/AGSSPA2/Claims+Architecture?preview=/110415945/110416877/Claims.Architecture.pdf">architecture</a> of the project. It's formed by the following components:
<ul>
  <li>FrontEnd Client</li>
  <li>Claims Backend API</li>
  <li>WebXl Backend API</li>
  <li>Notification Windows Service</li>
  <li>Workflow Windows Service</li>
  <li>Background Windows Service</li>
  <li>AGRE Document Manager API</li>
  <li>EWS Windows Service</li>
  <li>Content Manager Backend API</li>
</ul>
The <strong>"FrontEnd Client"</strong> sends HTTP Calls to "Claims Backend API" and receives push notifications via SignalR sent from "Notification Windows Service".<br />
The <strong>"Claims Backend API"</strong> doesn't process all the business rules. For some operations, it comunicates with "Workflow Windows Service", "Background Windows Service" and "EWS Windows Service" using a message queue in software RabbitMQ to pass the responsability of some business operations to these other services.
<br />
The <strong>"WebXl Backend API"</strong> is called from the "Claims Backend API" and "Workflow Windows Service" when it's necessary to download information from WebXl Oracle database.
<br />
The <strong>"EWS Windows Service"</strong> is continuosly listening to an specific Outlook Mailbox to receive Emails and send them to "Workflow Windows Service" in order to convert these Emails into database information. Also, each time any component needs to send an email, it communicates with EWS Windows Service via RabbitMQ to pass this responsability to this service.
<br />
The <strong>"Background Windows Service"</strong> performs some background operations as for example, generate the Loss Advice Documents to avoid keeping the user waiting for the API to generate them.
<br />
The <strong>"Content Manager Backend API"</strong> is used to store the file attachments of the incoming/outgoing emails that go through EWS Windows Service to avoid creating a RabbitMQ message that includes the file attachments, because it can provoke some performance issues.
<br />
The <strong>"AGRE Document Manager API"</strong> is called from all the other services to store any file document in a binary database.

## Software Installation

In this section we will explain how to install and configure the Claims Tool application on a local dev computer.<br />
<strong>IMPORTANT:</strong> The application only works with Windows computers.

### Prerequisites

This is the list of Software to install in your computer to launch the application:
    
* <strong>Visual Studio Code</strong>. (Download it and folow standard installation instructions from <a href="https://code.visualstudio.com/download">here</a>)
* <strong>Visual Studio 2019 PROFESSIONAL edition</strong>. (Download it and folow standard installation instructions from <a href="https://visualstudio.microsoft.com/es/vs/older-downloads/ ">here</a>)
* <strong>SQL Server DEVELOPER edition</strong>. (Download it and folow standard installation instructions from <a href="https://www.microsoft.com/es-es/sql-server/sql-server-downloads">here</a>)
* <strong>SQL Server Management Studio</strong>. (Download it and folow standard installation instructions from <a href="https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15">here</a>)
* <strong>Oracle SQL Developer</strong>. (Download it and folow standard installation instructions from <a href="https://www.oracle.com/tools/downloads/sqldev-downloads.html">here</a>)
* <strong>Rabbit MQ</strong>:
  1. Download it from here: https://www.rabbitmq.com/
  2. Follow the standard installation instructions. (Don't forget to install ERLANG when it asks for it)
  3. Enable the RabbitMQ Management Plugin executing the following instruction in a Command Line (Admin privileges):
  ```sh
   rabbitmq-plugins enable rabbitmq_management
   ```
  4. Open a browser and navigate to `http://localhost:15672` (access with default user/password `guest\guest`).
  5. Go to the `admin` tab and create a new user `claimsuser` with password `Axa12345`. Ensure that the user can access all virtual hosts.

![RabbitMQ Screen Shot][rabbit-screenshot]

<strong>IMPORTANT:</strong> Contact your technical lead to get a license key for Visual Studio Professional and Jetbrains Resharper 

<p align="right">(<a href="#top">back to top</a>)</p>

### Cloning the repositories

1. Clone the Claims Tool Project Repository
   ```sh
   git clone https://agpc-azureforge.visualstudio.com/Actor.Claims/_git/Claims
   ```
2. Clone the WebXl API repository
   ```sh
   git clone https://agpc-azureforge.visualstudio.com/Actor.Claims/_git/AXA.AGRE.WebXl
   ```
3. Clone the ContentManager API Repository
   ```sh
   git clone https://agpc-azureforge.visualstudio.com/Actor.Claims/_git/Claims.ContentManager
   ```
4. Clone the AGRE Content Manager API Repository
   ```sh
   git clone https://agpc-azureforge.visualstudio.com/Actor.Claims/_git/FileNet.Replacement
   ```
5. Ensure to Checkout the development branch for each of the repositories.
  ```sh
   git checkout development
   ```

<p align="right">(<a href="#top">back to top</a>)</p>

### Creating the databases
1. Create the following databases in your local Sql Server:
    <ul>
      <li>Claims</li>
      <li>AGREDocumentManager</li>
      <li>ClaimsContentManager</li>
    </ul>


![Databases Screen Shot][databases-screenshot]
  
2. Create a new Login and grant db_owner permissions to the newly created three databases.
   ```sh
   USE master
   GO
   CREATE LOGIN [Your_login_name] WITH PASSWORD = '[Your_Password]';
   ```
   ```sh
   USE AGREDocumentManager
   GO
   CREATE USER [Your_User_name] FOR LOGIN [Your_login_name]
   GO
   ALTER ROLE [db_owner] ADD MEMBER [Your_User_name]
   ```
   ```sh
   USE ClaimsContentManager
   GO
   CREATE USER [Your_User_name] FOR LOGIN [Your_login_name]
   GO
   ALTER ROLE [db_owner] ADD MEMBER [Your_User_name]
   ```
   ```sh
   USE Claims
   GO
   CREATE USER [Your_User_name] FOR LOGIN [Your_login_name]
   GO
   ALTER ROLE [db_owner] ADD MEMBER [Your_User_name]
   ```
3. Create the Database objects for each of the three DBs:
   1. For `Claims` Database, ask your tech lead to restore a Backup from QA environment.
   2. For `AGREDocumentManager` Database, execute all the scripts in folder `AGREDocumentManager\src\Deploy\Scripts\1.0.0`
   3. For `ClaimsContentManager` Database, execute all the scripts in folder `ClaimsContentManager\Deploy\Scripts\1.0.0`
4. Execute the following Scripts in <strong>`Claims`</strong> database:
   ```sh
   INSERT INTO [claims].ClaimsToolUser VALUES (NEWID(), '[Your_Email]', '[First_Name]', '[Last_Name]', 2, NULL, NULL, 3, 2, NULL, NULL, DATEADD(y,1,GETDATE()))
   GO
   INSERT INTO [claims].RfClaimsToolUserProfile VALUES((SELECT Id FROM [claims].ClaimsToolUser WHERE Email = 'Your_Email'), 'E7CE5243-14D3-45A4-9641-CE67C7DC887B')
   ```

<p align="right">(<a href="#top">back to top</a>)</p>

## Software Configuration

Now we are going to see how to set up configuration files and launch the application.

### Setting up configuration files

1. Open `web.config` file in `AXA.Claims.Workflow.Presentation.Api` and configure the following values:
  
   ![Claims Config Screen Shot][claimsconfig-screenshot]

2. Open `app.config` file in `AXA.Claims.Workflow.Presentation.WindowsService` and configure the following values:

   ![Workflow App Config Screen Shot][workflowappconfig-screenshot]

3. Open `app.config` file in `AXA.Claims.Ews.WindowsService` and configure the following values:

   ![EWS App Config Screen Shot][ewsappconfig-screenshot]

4. Open `app.config` file in `AXA.AGRE.Claims.Background.WindowsService` and configure the following values:

   ![Background App Config Screen Shot][backgroundappconfig-screenshot]

5. Open `web.config` file in `AXA.AGRE.DocumentManager.WebApi` and configure the following values:

   ![Document Manager Web Config Screen Shot][documentwebconfig-screenshot]

6. Open `web.config` file in `AXA.AGPC.Claims.ContentManager.WebApi` and configure the following values:

   ![Content Manager Web Config Screen Shot][contentmanagerwebconfig-screenshot]

### Installing the packages

Open Visual Studio Code and install all the NPM packages. This operation can take some time.

  ```sh
  npm install
  ```

In Visual Studio 2019, open `Tools > NuGet Package Manager > Package Manager Console` and restore all the nuget packages.

  ```sh
  nuget restore
  ```

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- LAUNCHING THE APP -->
## Launching the application

1. In Visual Studio Code, launch the Client Web Application

  ```sh
  npm start
  ```
2. Launch the following projects in Visual Studio 2019:

- [ ] AXA.Claims.Workflow.Presentation.Api
- [ ] AXA.Claims.Workflow.Presentation.WindowsService
- [ ] AXA.Claims.Ews.WindowsService
- [ ] AXA.AGPC.Notification.WindowsService
- [ ] AXA.AGRE.Claims.Background.WindowsService
- [ ] AXA.AGRE.DocumentManager.WebApi
- [ ] AXA.AGRE.WebXl.WebApi
- [ ] AXA.AGPC.Claims.ContentManager.WebApi

3. Navigate to `http://localhost:8907` to access the application.

<p align="right">(<a href="#top">back to top</a>)</p>


<!-- LICENSE -->
## License

 Copyright (c) 2018 AXA Shared Services Spain S.A.
 
 Licensed under the AXA Shared Services Spain S.A. License (the "License"); you
 may not use this file except in compliance with the License.
 A copy of the License can be found in the LICENSE.TXT file distributed
 together with this file.
 
 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.
 

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

Marc Carafí - marc.carafi@axa.com
Azucena Casado - azucena.casado@axa.com
Eduard Trapero - eduard.trapero@axa.com


<p align="right">(<a href="#top">back to top</a>)</p>


<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[product-screenshot]: images/screenshot.png
[databases-screenshot]: images/databases.png
[rabbit-screenshot]: images/rabbitmq.png
[claimsconfig-screenshot]: images/claimswebconfig.png
[workflowappconfig-screenshot]: images/workflowappconfig.png
[ewsappconfig-screenshot]: images/ewsappconfig.png
[backgroundappconfig-screenshot]: images/backgroundappconfig.png
[documentwebconfig-screenshot]: images/documentwebconfig.png
[contentmanagerwebconfig-screenshot]: images/contentmanagerwebconfig.png
[logo-screenshot]: images/logo.png
