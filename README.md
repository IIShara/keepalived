# keepalived
```
global_defs {
  enable_script_security
}

vrrp_script checkscript {
  script "/etc/keepalived/check.sh"       //Если скрипт возвращает 0, сервер работет. Если 1 сервер останавливается
  interval 3
  weight -20
}
vrrp_instance VI_1 {
    state MASTER                          //Назначен главным сервером. BACKUP - резервный сервер 
    interface ens160                      //Сетевой интерфейс, можно посмотреть при помощи ifconfig
    virtual_router_id 5                   //На всех серверах должен совпадать
    priority 101                          //чем больше значение, тем выше приоритет
    advert_int 1                          //VRRP Цикл многоадресной передачи секунд
    nopreempt
    notify /etc/keepalived/keepalived-notify.sh root
    authentication {
        auth_type PASS                    //VRRP метод аутентификации, ведущий и ведомый должны быть согласованы
        auth_pass 1111
    }
    unicast_peer {
       10.70.42.24                        //ip второго сервера
    }

    virtual_ipaddress {
        10.70.40.249/24 dev ens160 label ens160:vip
    }
    track_script {
        checkscript
    }
}
```
