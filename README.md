# docker-shibboleth-sp
Shibboleth SP with Apache running as reverse proxy in docker. Place this in front of the service you want to protect with Shibboleth.

Modify the shibboleth2.xml and 000-default.conf files in shibboleth/media to suit your configuration. Set the environment variables at the start of the dockerfile to corresponding values for your service and domain.

**Some knowledge of shibboleth, docker and apache required.***
