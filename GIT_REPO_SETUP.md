# List of Repositories Names:
## Backend:
-1 user-management
- device-management
- tenant-management
- fota-management
- fota-upgrade-management
- notification-management
- common-dependencies
- Common-utilities
## Mobile:
Android-mobile-app
ios-mobile-app 

How To clone and Build the project(Backend) :
Please write the email to IT team  to get the repo access 
Install java 17 and maven 3.8.6
Git clone <repo Name>
Open ~/.m2/settings.xml file and add the following
<profiles>
    <profile>
      <id>github</id>
      <repositories>
        <repository>
          <id>github</id>
       <url>https://maven.pkg.github.com/things-universe/common-dependencies</url>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </repository>
      </repositories>
    </profile>
  </profiles>
<servers>
    <server>
      <id>github</id>
      <username>{userName}</username>
      <password>{token}</password>
    </server>
  </servers>

* Note:Generate token via gitHub console 
Maven clean install and Run the project 









