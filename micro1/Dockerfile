FROM mhart/alpine-node:latest

# docker mantainer
MAINTAINER Ali Fathieh

RUN mkdir /src

# Add package.json and npm install to use docker cache for speedy build
ADD package.json /src/package.json
RUN cd /src && npm install

ADD . /src
WORKDIR /src

EXPOSE 3000
EXPOSE 5858

CMD ["npm", "start", "--", "--port", "3000"]

