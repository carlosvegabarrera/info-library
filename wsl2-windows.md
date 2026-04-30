# WSL2 

## DEBIAN
### Comando para permitir ejecutar el ping
**Error:**
> ping: socktype: SOCK_RAW  
> ping: socket: Operation not permitted  
> ping: => missing cap_net_raw+p capability or setuid?

**Solución:**
```shell
sudo setcap cap_net_raw+ep /bin/ping
```
