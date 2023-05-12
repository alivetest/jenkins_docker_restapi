FROM python:3.10-alpine AS build
# COPY api_pass.py wsgi.py uwsgi_conf.ini requirements.txt /pyapp/
COPY ./app_restapi/ /pyapp/
ENV PYTHONPATH=/pyapp
WORKDIR /pyapp
RUN apk add --no-cache gcc python3-dev libc-dev linux-headers
RUN pip3 install --no-cache-dir -r requirements.txt && pip3 install --no-cache-dir wheel uwsgi 

FROM python:3.10-alpine
COPY --from=build /pyapp/ /pyapp/
COPY --from=build /usr/local/bin/uwsgi /usr/local/bin/uwsgi
WORKDIR /pyapp
RUN pip3 install --no-cache-dir -r requirements.txt

# Jenkins don't work with ENTRYPOINT must use CMD instead
# ENTRYPOINT ["/usr/local/bin/uwsgi", "--ini", "/pyapp/uwsgi_conf.ini"]
# ENTRYPOINT ["/usr/local/bin/uwsgi"]
CMD ["/usr/local/bin/uwsgi", "--ini", "/pyapp/uwsgi_conf.ini"]
EXPOSE 5000
