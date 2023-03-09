# List of Repositories Names:
## Backend:
1. user-management
2. device-management
3. tenant-management
4. fota-management
5. fota-upgrade-management
6. notification-management
7. common-dependencies
8. Common-utilities
## Mobile:
1. Android-mobile-app
2. ios-mobile-app 

## How To clone and Build the project(Backend) :
1. Please write the email to IT team  to get the repo access 
2. Install java 17 and maven 3.8.6
3. Git clone <repo Name>
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

 **Note**:Generate token via gitHub console 
##### Maven clean install and Run the project 









