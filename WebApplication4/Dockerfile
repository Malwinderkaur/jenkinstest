FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim
MAINTAINER MalwinderKaur
WORKDIR /app
COPY app/publish .
ENTRYPOINT ["dotnet", "WebApplication4.dll"]
EXPOSE 80
EXPOSE 443