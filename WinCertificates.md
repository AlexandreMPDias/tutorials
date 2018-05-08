# Creating your own local Certificate

Tutorial created based on [Spiceworks](https://community.spiceworks.com/how_to/122368-signing-a-powershell-script-with-a-self-signed-certificate)

-------

## Requirements

The only requirement to digitally sign your own code, is signtool.exe present on [Windows SDK](http://www.microsoft.com/en-us/download/confirmation.aspx?id=8279)  
  
*PS:* Make sure that signtool.exe's directory is placed on your System Path.  
Look for *signtool* here:  
- "C:\Program Files (x86)\Windows Kits"
- "C:\Program Files (x86)\Microsoft SDK"

## Single-script
  
The following code:  
- Generates an CA certificate  
- Adds the generated CA certificate to the Trusted-Certificates store.  
- Generates a public certificate out of the CA certificate.  
- Signs an executable.  

Edit the following code and save it on a .bat file  
  
  ```.bat
    @echo off
    setlocal
    set "companyname=CyberLabs"
    set "privatekey=cyberlabs"
    set "CACert=cyberlabsCA"
    set "certificate=cyberlabs"

    :Don't edit anything under here

    : Creates an CA Certificate
    makecert -n "CN=%companyname%" -r -sv %privatekey%.pvk %certificate%_ca.cer

    : Adds the CA Certificate to the Trusted certificates Store
    CERTUTIL -addstore -enterprise -f -v root "%certificate%_ca.cer"

    : Generate a public certificate
    makecert -sk %companyname% -iv %privatekey%.pvk -n "CN=%companyname%" -ic %certificate%_ca.cer %certificate%.cer -sr currentuser -ss My

    : Sign the code with the generated certificate
    gci cert:\CurrentUser\My -codesigning
    signtool sign /a %1
  ```
  
  Usage is:
  ```.bat
  scriptname.bat path\to\main.exe 
  ```
