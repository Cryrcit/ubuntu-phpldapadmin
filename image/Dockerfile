FROM cryrcit/ubuntu-web-baseimage:20.04_1.0.0

# phpLDAPadmin version
ARG PHPLDAPADMIN_VERSION=1.2.6.3

RUN apt-get -y update \
#add repository for php ################################
    && LC_ALL=C DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    software-properties-common \
    && add-apt-repository ppa:ondrej/php 
########################################################

# Add multiple process stack to supervise apache2 and php7.3-fpm
# Install ca-certificates, curl and php dependencies
# Download phpLDAPadmin, check file integrity, and unzip phpLDAPadmin to /var/www/phpldapadmin_bootstrap
# Remove curl
RUN apt-get update \
	&& /container/tool/add-multiple-process-stack \
	&& /container/tool/add-service-available :apache2 :php7.3-fpm :ssl-tools \
	&& LC_ALL=C DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
	ca-certificates \
	curl \
 	php7.3-ldap \
 	php7.3-readline \
 	php7.3-xml \
	&& curl -o phpldapadmin.tar.gz -SL https://github.com/leenooks/phpLDAPadmin/archive/${PHPLDAPADMIN_VERSION}.tar.gz \
	&& mkdir -p /var/www/phpldapadmin_bootstrap /var/www/phpldapadmin \
	&& tar -xzf phpldapadmin.tar.gz --strip 1 -C /var/www/phpldapadmin_bootstrap \
	&& apt-get remove -y --purge --auto-remove curl ca-certificates \
	&& rm phpldapadmin.tar.gz \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Add service directory to /container/service
ADD service /container/service

# Use baseimage install-service script
RUN /container/tool/install-service

# Add default env variables
ADD environment /container/environment/99-default

# Set phpLDAPadmin data directory in a data volume
VOLUME ["/var/www/phpldapadmin"]

# Expose http and https default ports
EXPOSE 80 443
