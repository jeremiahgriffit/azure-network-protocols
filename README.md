# Creating Users, Managing Group Policy, and Accounts

<p align="center">
<img src="https://github.com/user-attachments/assets/0bb09e28-c161-451f-a72d-9e0315fc433d" alt="Microsoft Active Directory Logo"/>
</p>


<h1>Creating Users, Managing Group Policy, and Accounts</h1>
In this lab we will be Adding users and Managing Group Policy 

## Previous Steps

[Deploying Active Directory in Azure](https://github.com/jeremiahgriffit/Deploying-Active-Directory-in-Azure/)

<h2>Operating Systems Used </h2>

- Windows Server 2022 Datacenter: Azure Edition - x64 Gen2  
- Windows 10 Pro, version 22H2 - x64 Gen2  

<h2>High-Level Deployment and Configuration Steps</h2>

1. Allow Domain users to access remote desktop
2. Run Powershell script to add 1000 users to our domain
3. Create a Group Policy that will lock users out if they fail to login 5 times
4. Reset users passwords
5. Enabling and Disabling users


<h2>Deployment and Configuration Steps</h2>

### Allow Domain users to access remote desktop
#### Connect to Client-1 using admin jane_admin

Find client-1 public IP address:  
Virtual Machines > Client-1 > Networking > Network settings 

![image](https://github.com/user-attachments/assets/35965722-d05f-4c90-be6c-a737d71cd161)


login using Remote Desktop Connection 

Make sure to login using domain context:
Username: EXAMPLEDOMAIN\jane_admin  
Password: LabPassword123

![1](https://github.com/user-attachments/assets/9c63688e-c4d1-4e88-8a89-51f1cc083a79)


Inside Client-1 Right click windows icon and lick "System"

![2](https://github.com/user-attachments/assets/aefe1fff-8043-477f-b5aa-df8eb360dd01)

Click on "Remote Desktop" 

![3-cropped-resized](https://github.com/user-attachments/assets/656515c0-3079-4c75-a0b0-037804794971)

Click on "Select users that can remotely access this PC"  

![4](https://github.com/user-attachments/assets/87fad318-4313-442d-9686-98934a81d4dd)

Click "ADD"

![5](https://github.com/user-attachments/assets/9c895dfc-6a48-4628-86ad-3d2fda6f8783)

Type "Domain Users" and click "OK"  

![6](https://github.com/user-attachments/assets/4f7b37f6-dfce-4563-ad13-3f0514b45bdd)

Now we have configured that all domain users can remotely access this client

### Create Users

#### Login to Domain-Controller-1

#### Create users using powershell Script

Open "Powershell ISE" and run it as Admin

![9](https://github.com/user-attachments/assets/0a24286e-2123-44f1-96b5-e22cd18f66ae)


Then copy and paste this script into Powershell ISE:

```
 # ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "LabPassword123"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 1000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name

}

$count = 1
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $fisrtName = generate-random-name
    $lastName = generate-random-name
    $username = $fisrtName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}

```

It should look like this: 

![image](https://github.com/user-attachments/assets/9440e721-ea7f-49e7-8450-a8875f8e856b)

Press the green run button to run the script"

![8](https://github.com/user-attachments/assets/c42cf480-6785-4db6-b10d-782f3aea9de3)

Now the script will make 1000 random users with the password: LabPassword123

### Verify that users were created

Navigate to "Active Directory Users and Computers"  
![10](https://github.com/user-attachments/assets/657fa35d-c384-453f-be04-7172b9d812f3)

Navitage to _EMPLOYEES OU and verify user accounts were created

![10](https://github.com/user-attachments/assets/30be5521-8a2a-4612-a5e7-f16943003a2a)

### Creating an account lockout based on failed log in attempts

Press windows key + r and type in "gpmc.msc" and click ok

![image](https://github.com/user-attachments/assets/a57eba77-f82e-43f3-a10b-ff85729860a7)

Next right click on "Default Domain Policy" and click "Edit"

![11](https://github.com/user-attachments/assets/7b9459af-4d58-42ff-a79a-f3e423919cbe)

This will bring up the Group Policy Management Editor navitgate to:
Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy


![12](https://github.com/user-attachments/assets/7a3935e9-074b-4635-ad9f-9b4cca6b92a2)

Double click "Account Lockout Duration" and set it to 30mins

![13](https://github.com/user-attachments/assets/1c1a57c2-ea38-4be0-bf81-667f3440e8c4)

One we do that and click apply windows will automatically set the failed login attempts to 5 attempts before a lockout occurs

![14](https://github.com/user-attachments/assets/30b92a95-90ee-4630-bc36-037435758359)


### Updating the group Policy 

Option 1 - Wait around 90 mins for the group policy to propagate automatically

Option 2 - Force update on the Client

Since we don't want to wait 90 mins for the group policy to propagate we will force the update manually 

#### Connect to Client-1 

Find client-1 public IP address:  
Virtual Machines > Client-1 > Networking > Network settings 

![image](https://github.com/user-attachments/assets/35965722-d05f-4c90-be6c-a737d71cd161)


login using Remote Desktop Connection

![15](https://github.com/user-attachments/assets/aa0dd5c3-2850-4e41-ba42-42ecdbc5bb0a)


Using command prompt we can force the update by using the command "gpupdate /force"

![image](https://github.com/user-attachments/assets/b76f4fc4-a106-445b-9035-50d99d2848e8)

#### Verifying the group policy updated

I ran the command "gpresult /r"   
I recived the following output and I verifyied the last time it was updated was only a few mins ago I took the screen shot the VM's Local time was 1:41 am

![image](https://github.com/user-attachments/assets/861da9dd-6ba1-4907-a90f-8360e0566fb5)

For testing purposes I will also test the group policy by using one of the randomly generated accounts and fail to login 5 times on purpose to test if the lock out works  
I chose the random user baso.fit

![image](https://github.com/user-attachments/assets/e5b4587d-3bc1-45cb-b8a8-f6eb6c272dfc)

Once I exceeded the 5 failed attempts I got this messaage verifying that the group policy updated and works!

![image](https://github.com/user-attachments/assets/49b1d7d0-7d38-4608-b0b9-7a02775814f3)

### Unlock the account 

Now that we locked the account lets go unlock it so user baso.fit can log in successfully

On Domain-Controller-1 find baso.fit in Active Directory Users and Computers  
Go to properties > tick "Unlock account" > click apply

![image](https://github.com/user-attachments/assets/0caa8a08-eea4-48b8-aa76-c1e01f8257db)

Now when I reconnect with the baso.fit user and correct password I can sucessfully log on!

![16](https://github.com/user-attachments/assets/5d3cfa06-e7d3-4a22-a807-573592a3640a)

### Resetting passwords

In a real enviroment this is somthing that is done frequently so it is good information to know

Using the find function I searched for baso.fit > right click > reset passwword

![17](https://github.com/user-attachments/assets/fc73a4af-686f-41c6-9a58-d15d7203c9fa)

From here we can reset the password as well as unlocking the users account if that is nessary 

![image](https://github.com/user-attachments/assets/b2da47a3-572c-43c4-8679-8e45285d66ab)

### Enabling and Disabling accounts 

From the same search function and user as before 

#### Disabling accounts

Once you find the right user > Right click > disable account
![18](https://github.com/user-attachments/assets/ba46e1d3-b104-440b-95de-d7db1fa328ad)

Once the account is disabled the icon should change to this:

![image](https://github.com/user-attachments/assets/2fd54fde-441a-47ea-a9c8-cb123641ce85)

#### Enabling 
Once you find the right user > Right click > enable account

![19](https://github.com/user-attachments/assets/3e8eab61-e94b-467c-b118-6c0a5540ab62)

Also will get this popup:

![image](https://github.com/user-attachments/assets/2a0572ce-7fba-4fd3-a4e6-274d58019e52)

### Observing Domaain Controller logs

press windows key + r and type "eventvwr.msc"

![image](https://github.com/user-attachments/assets/0bbef1ee-c697-474f-a4c9-4083b771f276)

Using the event viewer lets try to find all of the failed login attempts we did earlier using baso.fit user account 

We will look under:
Windows logs > security > find "baso.fit"

![image](https://github.com/user-attachments/assets/408e8c1c-14d0-4a83-9277-6cc761947c04)

here we can view the "audit failures" which are the failed login that lead to the account being locked earlier 

![image](https://github.com/user-attachments/assets/5314206e-c359-4cb2-97d7-d7165e87de32)

Thats all for this lab we allowed remote connections, added 1000 users leveraging a powershell script, and created a group policy!



