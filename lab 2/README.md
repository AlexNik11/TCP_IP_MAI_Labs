# Лабораторная работа 2
### Настройка на все роутеры
```

cat /proc/sys/net/ipv4/ip_forward
sudo bash -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'
sudo yum install nano -y
sudo nano /etc/sysctl.d/router.conf

net.ipv4.ip_forward = 1
net.ipv4.conf.all.forwarding = 1
net.ipv4.conf.all.rp_filter = 2


sudo yum install quagga -y
sudo touch /etc/quagga/ospfd.conf
sudo chown quagga:quagga /etc/quagga/ospfd.conf && sudo chmod 640 /etc/quagga/ospfd.conf

sudo systemctl enable zebra
sudo systemctl start zebra
sudo systemctl enable ospfd
sudo systemctl start ospfd

systemctl status firewalld
sudo systemctl start firewalld
sudo firewall-cmd --add-protocol=ospf --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```
### Первый роутер (R1)
```
sudo vtysh                    		# вход в quagga
configure terminal            		# вход режим конфигурации роутера
router ospf                   		# включение режима OSPF на роутере
router-id  10.0.0.1           		# присвоение id роутеру в сети OSPF
passive-interface default     		# выключение машрута по умолчанию
network 172.16.0.0/24 area 0  		# присвоение нулевой зоны для сети сети 172.16.0.0/24
no passive-interface eth1     		# включение интерфеса eth1, по умолчанию выключен
default-information originate 		# включение распространения маршрута по умолчанию
exit                          		# выход из режима настройки OSPF
exit                          		# выход в основной режим
copy running-config startup-config 	# сохранение конфигурации в память роутера, чтобы при запуске не приходилось каждый раз настраивать заново
exit                          		# выход в консоль

sudo iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE  # включение маскарада на интерфейсе eth1
```
![router1](router1.png)

### Второй роутер (R2)
```
sudo vtysh                    		# вход в quagga
configure terminal            		# вход режим конфигурации роутера
router ospf                   		# включение режима OSPF на роутере
router-id  10.0.0.2           		# присвоение id роутеру в сети OSPF
passive-interface default     		# выключение машрута по умолчанию
no passive-interface eth1     		# включение интерфеса eth1, по умолчанию выключен
network 172.16.0.0/24 area 0  		# присвоение area 0 сети 172.16.0.0/24
no passive-interface eth2     		# включение интерфеса eth2, по умолчанию выключен
network 172.16.1.0/24 area 1  		# присвоение area 1 сети 172.16.1.0/24
no passive-interface eth3     		# включение интерфеса eth3, по умолчанию выключен
network 172.31.0.0/24 area 0  		# присвоение area 0 сети 172.31.0.0/24
exit                          		# выход из режима настройки OSPF
exit                          		# выход в основной режим
copy running-config startup-config 	# сохранение конфигурации в память роутера, чтобы при запуске не приходилось каждый раз настраивать заново
```
![router2](router2.png)
### Третий роутер (R3)
```
sudo vtysh                     		# вход в quagga
configure terminal             		# вход режим конфигурации роутера
router ospf                    		# включение режима OSPF на роутере
router-id  10.0.0.3            		# присвоение id роутеру в сети OSPF
passive-interface default      		# выключение машрута по умолчанию
no passive-interface eth1      		# включение интерфеса eth1, по умолчанию выключен
network 172.16.0.0/24 area 0   		# присвоение area 0 сети 172.16.0.0/24
no passive-interface eth2      		# включение интерфеса eth2, по умолчанию выключен
network 192.168.0.0/24 area 0  		# присвоение area 1 сети 192.168.0.0/24
no passive-interface eth3      		# включение интерфеса eth3
network 172.31.0.0/24 area 0   		# присвоение area 0 сети 172.31.0.0/24
exit                           		# выход из режима настройки OSPF
exit                           		# выход в основной режим
copy running-config startup-config 	# сохранение конфигурации в память роутера, чтобы при запуске не приходилось каждый раз настраивать заново
```
![router3](router3.png)
### Четвёртый роутер (R4)
```
sudo vtysh                         	# вход в quagga
configure terminal                 	# вход режим конфигурации роутера
router ospf                        	# включение режима OSPF на роутере
router-id  10.0.0.4                	# присвоение id роутеру в сети OSPF
passive-interface default          	# выключение машрута по умолчанию
no passive-interface eth1          	# включение интерфеса eth1, по умолчанию выключен
network 172.16.1.0/24 area 1       	# присвоение area 0 сети 172.16.1.0/24
no passive-interface eth2          	# включение интерфеса eth2, по умолчанию выключен
network 192.168.1.0/24 area 1      	# присвоение area 1 сети 192.168.1.0/24
exit                               	# выход из режима настройки OSPF
exit                               	# выход в основной режим
copy running-config startup-config 	# сохранение конфигурации в память роутера, чтобы при запуске не приходилось каждый раз настраивать заново
```
![router4](router4.png)