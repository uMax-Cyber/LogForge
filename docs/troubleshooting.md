# Syslog Troubleshooting

## "Logs not arriving" checklist
1. Is the receiver listening? `ss -ulnp | grep 514`
2. Is the sender configured? Check device UI/API for remote syslog setting
3. Are packets arriving? `tcpdump -lni any udp port 514 -c 10`
4. Are they going to the right file? Check filter order and file permissions
5. Is rsyslog writing? Check journalctl for rsyslog errors

## "Permission denied" in rsyslog
The log file must be owned by `syslog:adm`:
```bash
chown syslog:adm /var/log/your-log-file.log
chmod 640 /var/log/your-log-file.log
```

## Firewall sourcing from unexpected IP
Multi-interface firewalls source from the egress interface for the syslog
destination. Sniff to find the actual source:
```bash
tcpdump -lni any udp port 514 and not src host <expected_ip>
```
