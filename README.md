# Fail2ban filters

This repository contains Fail2ban filters that aim to reduce log noise from scanners. Fail2ban is not a security solution, as it is only able to react after events occur. You should include specific protections against any serious queries before they occur.

## Filters

### Nginx HTTP no verb

The `nginx-http-no-verb` filter uses the expected syntax of the HTTP Request line to identify unexpected HTTP queries that were sent to Nginx.

For example, it aims to temporarily block scanners after they send queries similar to the following:

```
172.104.88.48 - - [06/Apr/2022:08:26:42 +0100] "\x16\x03\x00\x00i\x01\x00\x00e\x03\x03U\x1C\xA7\xE4random1random2random3random4\x00\x00\x0C\x00/\x00" 400 157 "-" "-" "-"
66.240.205.34 - - [06/Mar/2022:09:32:04 +0100] "145.ll|'|'|SGFjS2VkX0Q0OTkwNjI3|'|'|WIN-JNAPIER0859|'|'|JNapier|'|'|19-02-01|'|'||'|'|Win 7 Professional SP1 x64|'|'|No|'|'|0.7d|'|'|..|'|'|AA==|'|'|112.inf|'|'|SGFjS2VkDQoxOTIuMTY4LjkyLjIyMjo1NTUyDQpEZXNrdG9wDQpjbGllbnRhLmV4ZQ0KRmFsc2UNCkZhbHNlDQpUcnVlDQpGYWxzZQ==12.act|'|'|AA==" 400 157
192.241.224.88 - - [14/Apr/2022:03:26:07 +0100] "MGLNDD_112.102.252.52_443" 400 157 "-" "-" "-"
66.240.205.34 - - [14/Apr/2022:23:39:45 +0100] "Gh0st\xAD\x00\x00\x00\xE0\x00\x00\x00x\x9CKS``\x98\xC3\xC0\xC0\xC0\x06\xC4\x8C@\xBCQ\x96\x81\x81\x09H\x07\xA7\x16\x95e&\xA7*\x04$&g+\x182\x94\xF6\xB000\xAC\xA8rc\x00\x01\x11\xA0\x82\x1F\x5C`&\x83\xC7K7\x86\x19\xE5n\x0C9\x95n\x0C;\x84\x0F3\xAC\xE8sch\xA8^\xCF4'J\x97\xA9\x82\xE30\xC3\x91h]&\x90\xF8\xCE\x97S\xCBA4L?2=\xE1\xC4\x92\x86\x0B@\xF5`\x0CT\x1F\xAE\xAF]" 400 157 "-" "-" "-"
45.227.254.52 - - [15/Apr/2022:05:55:56 +0100] "\x03\x00\x00*%\xE0\x00\x00\x00\x00\x00Cookie: mstshash=Test" 400 157 "-" "-" "-"
```

It also matches the following attempts to use Nginx as an HTTP proxy. These cases are extremely unlikely to be legitimate, but if they are needed you may not want to use this filter or you may want to add additional ignore regexes to avoid blocking them.

```
45.148.10.81 - - [12/Apr/2022:20:25:30 +0100] "GET http://example.com/ HTTP/1.1" 444 0 "-" "Go-http-client/1.1" "-"
62.171.147.55 - - [15/Apr/2022:20:34:07 +0100] "CONNECT 62.171.147.55:884 HTTP/1.1" 400 157 "-" "-" "-"
```

## Installation

1. Copy the desired filter files from `fail2ban/filter.d/` to `/etc/fail2ban/filter.d/` in your server.
1. Copy the matching jail files from `fail2ban/jail.d/` to `/etc/fail2ban/jail.d/` in your server, modifying the `findtime`, `bantime`, and `maxretry` as desired.

### Testing

It is highly recommended to do testing on your actual log files before restarting fail2ban.

#### Nginx HTTP no verb

Run `fail2ban-regex --print-all-matched --print-all-ignored /var/log/nginx/access.log /etc/fail2ban/filter.d/nginx-http-no-verb.conf /etc/fail2ban/filter.d/nginx-http-no-verb.conf`

### Restart Fail2ban

After testing to ensure that there are no unexpected matches and no unexpected ignored lines, restart the fail2ban service.

For example, on Systemd systems this may be done using:

```bash
sudo systemctl restart fail2ban.service
```

## License

MIT
