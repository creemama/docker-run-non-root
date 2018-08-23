FROM creemama/run-non-root:1.0-certbot

# If necessary, install one or more of Certbot's DNS plugins.
# This snippet just installs the Route 53 plugin.
# https://certbot.eff.org/docs/using.html
# https://pypi.python.org/pypi/pip
# https://pypi.python.org/pypi/certbot-dns-route53
RUN pip install --upgrade 'pip==18.0' \
 && pip install certbot-dns-route53==0.26.1

# Run the cron every day at midnight and noon.
# https://certbot.eff.org/#pip-other recommends renewing twice a day.
RUN echo "0  0,12  *  *  *  run-non-root-certbot" > /etc/crontabs/root

ENV RUN_NON_ROOT_COMMAND "certbot renew"

ENTRYPOINT ["tini"]

# -l 2 sets the log level to 2.
# -f runs crond in the foreground.
CMD ["--", "crond", "-f", "-l", "2"]
