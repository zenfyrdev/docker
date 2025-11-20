FROM mcr.microsoft.com/dotnet/sdk:10.0-alpine AS build

RUN mkdir /src /app

WORKDIR /src
COPY ./AppViewLite .
RUN dotnet publish src/AppViewLite.Web -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:10.0-alpine

RUN apk add --no-cache \
    git

RUN mkdir /app
VOLUME [ "/home/app/BskyAppViewLiteData", "/home/app/.aspnet/DataProtection-Keys" ]

WORKDIR /app
COPY --from=build /app/publish .

RUN chown -R app:app /app /home/app

USER app

ENV APPVIEWLITE_BIND_URLS=http://+:8080
ENV APPVIEWLITE_ALLOW_NEW_DATABASE=1
EXPOSE 8080

ENTRYPOINT ["dotnet", "AppViewLite.Web.dll"]
