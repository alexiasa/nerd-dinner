# escape= `

FROM microsoft/iis

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop';"]

# Install ASP.NET
RUN Install-WindowsFeature NET-Framework-45-ASPNET; `
	Install-WindowsFeature Web-Asp-Net45


# Website setup - expose port, make directories, create new website with defined port and path to files, define App Pool

EXPOSE 8081

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


# REMARK: Temporary workaround for Windows DNS client weirdness - will always look up addresses from the DNS server, which is the Docker Engine in this case
RUN set-itemproperty -path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name ServerPriorityTimeLimit -Value 0 -Type DWord

ENV BING_MAPS_KEY `
    IP_INFO_DB_KEY `
    AUTH_DB_CONNECTION_STRING `
    APP_DB_CONNECTION_STRING


COPY .\mvc4\NerdDinner\ c:/nerd-dinner/
COPY .\bootstrap.ps1 /

ENTRYPOINT /bootstrap.ps1