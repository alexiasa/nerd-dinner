# this requires an intermediate msbuild container to compile the .net application!
# it's basically steps 1 and 2 of the process detailed here: http://blog.alexellis.io/3-steps-to-msbuild-with-docker/
# The code's in the msbuild-Dockerfile. Build and run that first! Then follow step 2, substituting with the proper project/solution names 

# escape=`

# start w/ IIS base, start PowerShell, and install ASP.NET stuff
FROM microsoft/iis:10.0.14393.206
SHELL ["powershell", "-command"]

RUN Install-WindowsFeature NET-Framework-45-ASPNET; `
	Install-WindowsFeature Web-Asp-Net45

# install the SQL server 2012 LocalDB via web request
RUN Invoke-WebRequest -OutFile c:\SqlLocalDb.msi -Uri http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SqlLocalDB.MSI

# super weird CLI stuff for unattended installation of aforementioned SQL server - don't mess with this
RUN ["cmd", "/S", "/C", "c:\\windows\\syswow64\\msiexec", "/i", "c:\\SqlLocalDB.msi", "IACCEPTSQLLOCALDBLICENSETERMS=YES", "/qn"]

# start LocalDB
RUN & 'C:\Program Files\Microsoft SQL Server\110\Tools\Binn\sqllocaldb' start "v11.0"

EXPOSE 8081

# Website setup - make directories, create new website with defined port and path to files, define App Pool
RUN Remove-Website -Name 'Default Web Site'; `
	md c:\nerd-dinner; `
	md c:\databases; `
	New-Website -Name 'nerd-dinner' `
				-Port 8081 -PhysicalPath 'c:\nerd-dinner' `
        -ApplicationPool '.NET v4.5'

# Custom App Pool settings to let IIS use LocalDB
RUN Import-Module WebAdministration; `
  Set-ItemProperty 'IIS:\AppPools\.NET v4.5' -Name 'processModel.loadUserProfile' -Value 'True'; `
  Set-ItemProperty 'IIS:\AppPools\.NET v4.5' -Name 'processModel.setProfileEnvironment' -Value 'True'

# Unlock custom webserver handler settings
RUN & c:\windows\system32\inetsrv\appcmd.exe `
      unlock config `
      /section:system.webServer/handlers

COPY NerdDinner c:\nerd-dinner


