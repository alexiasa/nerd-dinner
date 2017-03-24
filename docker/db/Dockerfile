
# escape= `

FROM microsoft/mssql-server-windows-express
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop';"]

EXPOSE 1433
VOLUME c:\\database
ENV sa_password S0000_N3rdy

WORKDIR c:\\init

COPY . .

CMD ./Initialize-Database.ps1 -sa_password $env:sa_password -db_name NerdDinner -Verbose
