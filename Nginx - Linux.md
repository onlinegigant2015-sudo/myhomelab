# Nginx

Nginx -это веб-сервер, который позволяет клиентам взаимодействовать с сайтами.  
Основные настройки данного веб-сервера хранятся по пути `/etc/nginx/nginx.conf`. Настройка самих сайтов описывается в конфигурационных файлах по пути `/etc/nginx/sites-available/`. Эти файлы состоят из настроек по какому порту будет прослушиваться сайт, какое имя сервера (`server_name`) должно быть, где сайт должен искать ресурсы, и так далее.  
Файл `/etc/nginx/nginx.conf` смотрит в путь `/etc/nginx/sites-enabled/` и включает файлы по этому пути в себя. В директории `/etc/nginx/sites-enabled/` находятся символьные ссылки (ярлыки) на файлы директории `/etc/nginx/sites-available/`, Это гениальное решение `Ubuntu`, которое позволяет быстро отключать сайты, просто удалив ярлык в `/etc/nginx/sites-enabled/`.

## Reverse Proxy

Reverse Proxy - это механизм "тайной" перессылки на определенный адрес и порт.  
Золотой стандарт настройки файла конфигурации для Reverse Proxy

```nginx
server {    
    listen <listening port>;    
    server_name _;    
    
    location / {    
        # Перенаправляем все запросы на определенный адрес и порт    
        proxy_pass http://<ip address>:<local port>;    
    
        # Передаем правильные заголовки, чтобы Python-сервер знал, кто его просит    
        proxy_set_header Host $host;    
        proxy_set_header X-Real-IP $remote_addr;    
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;    
    }    
}
```

 
