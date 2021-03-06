FROM baozhida/test-nodebase:3
MAINTAINER baozihda <baozihda88@126.com>

USER root
                                                                                                                      
#============================================                                                                                                     
# 修改host，紧接着下载Google Chrome                                                                                                                             
#============================================                                                                                                     
# can specify versions by CHROME_VERSION;                                                                                                         
#  e.g. google-chrome-stable=53.0.2785.101-1                                                                                                      
#       google-chrome-beta=53.0.2785.92-1                                                                                                         
#       google-chrome-unstable=54.0.2840.14-1                                                                                                     
#       latest (equivalent to google-chrome-stable)                                                                                               
#       google-chrome-beta  (pull latest beta)                                                                                                    
#============================================                                                                                                     
#ARG CHROME_VERSION="google-chrome-stable"
RUN wget --no-verbose -O hosts https://raw.githubusercontent.com/baozhida/docker-selenium/master/NodeChrome/hosts \
  && cp ./hosts /etc/hosts \
  && wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
  && echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list \
  && apt-get update -qqy \
  && apt-get -qqy install google-chrome-stable \
  && rm /etc/apt/sources.list.d/google-chrome.list \
  && rm -rf /var/lib/apt/lists/* /var/cache/apt/* \
  && rm hosts

#==================
# Chrome webdriver
#==================
ARG CHROME_DRIVER_VERSION=2.29
RUN wget --no-verbose -O hosts https://raw.githubusercontent.com/baozhida/docker-selenium/master/NodeChrome/hosts \
  && cp ./hosts /etc/hosts \
  && wget --no-verbose -O /tmp/chromedriver_linux64.zip https://chromedriver.storage.googleapis.com/$CHROME_DRIVER_VERSION/chromedriver_linux64.zip \
  && rm -rf /opt/selenium/chromedriver \
  && unzip /tmp/chromedriver_linux64.zip -d /opt/selenium \
  && rm /tmp/chromedriver_linux64.zip \
  && mv /opt/selenium/chromedriver /opt/selenium/chromedriver-$CHROME_DRIVER_VERSION \
  && chmod 755 /opt/selenium/chromedriver-$CHROME_DRIVER_VERSION \
  && ln -fs /opt/selenium/chromedriver-$CHROME_DRIVER_VERSION /usr/bin/chromedriver \
  && rm hosts

#========================
# Selenium Configuration
#========================
# As integer, maps to "maxInstances"
ENV NODE_MAX_INSTANCES 1
# As integer, maps to "maxSession"
ENV NODE_MAX_SESSION 1
# As integer, maps to "port"
ENV NODE_PORT 5555
# In milliseconds, maps to "registerCycle"
ENV NODE_REGISTER_CYCLE 5000
# In milliseconds, maps to "nodePolling"
ENV NODE_POLLING 5000
# In milliseconds, maps to "unregisterIfStillDownAfter"
ENV NODE_UNREGISTER_IF_STILL_DOWN_AFTER 60000
# As integer, maps to "downPollingLimit"
ENV NODE_DOWN_POLLING_LIMIT 2
# As string, maps to "applicationName"
ENV NODE_APPLICATION_NAME ""

COPY generate_config /opt/selenium/generate_config
RUN chmod +x /opt/selenium/generate_config

#=================================
# Chrome Launch Script Modication
#=================================
COPY chrome_launcher.sh /opt/google/chrome/google-chrome
RUN chmod +x /opt/google/chrome/google-chrome

RUN chown -R uiauto:uiauto /opt/selenium

# Following line fixes
# https://github.com/SeleniumHQ/docker-selenium/issues/87
RUN echo "DBUS_SESSION_BUS_ADDRESS=/dev/null" >> /etc/environment

USER uiauto
