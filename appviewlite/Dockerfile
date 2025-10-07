FROM mcr.microsoft.com/dotnet/sdk:9.0-alpine AS build

RUN mkdir /src /app

WORKDIR /src
COPY ./AppViewLite .
RUN dotnet publish src/AppViewLite.Web -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:9.0-alpine

RUN apk add --no-cache \
    git

RUN mkdir /app
VOLUME [ "/home/app/BskyAppViewLiteData", "/home/app/.aspnet/DataProtection-Keys" ]

WORKDIR /app
COPY --from=build /app/publish .

RUN chown -R app:app /app /home/app

USER app

ENV APPVIEWLITE_BIND_URLS=http://localhost:61750
EXPOSE 61750

ENTRYPOINT ["dotnet", "AppViewLite.Web.dll"]