*******
Лабораторная работа №1: Мониторинг инфраструктуры с помощью Prometheus и Grafana
*******
Цель работы: Настроить сбор метрик системы с Managed Node с помощью Node Exporter, их хранение и агрегацию в Prometheus, а также визуализацию в Grafana.

Используемый стек: Docker, Prometheus, Node Exporter и Grafana.

Структура проекта:
- playbooks/ - Ansible плейбуки для автоматизации развертывания.
- configs/ - файлы конфигурации (например, для Prometheus).
- inventory/ - inventory-файл для Ansible.

В данной работе используется простейшая схема с 2 узлами и коммутатором между ними:
<img width="682" height="166" alt="Image" src="https://github.com/user-attachments/assets/9b6dfc6d-3c9e-4312-bee7-246c8674d23d" />

Где Control Node (управляющий узел) - это компьютер, на котором установлен Ansible и с которого происходит управление другими машинами (узлами) в сети.

Managed Node (управляемый узел) - это конечная точка для автоматизации, на которой выполняются действия, определённые в плейбуках Ansible.

Для выполнения работы требуется:

1) Настроить IP адреса для Control Node и Managed Node через netplan, nmtui и т.д. В данном примере используются адреса 192.168.50.10 и 192.168.50.5 соответственно.
2) На Control Node необходимо настроить inventory файл (в данном примере все inventory файлы хранятся по пути SRE-lab/inventory/hosts, а плейбуки - SRE-lab/playbooks/).
3) Необходимо создать ssh ключ на Control Node:
   ```bash
   ssh-keygen -t rsa -b 4096 
   ssh-copy-id user_name@192.168.50.5
4) На Managed Node предоставить пользователю права на выполнение команд sudo без пароля (ТОЛЬКО для учебных стендов):
   ```bash
   sudo visudo 
   user_name ALL=(ALL) NOPASSWD: ALL
5) Необходимо установить Docker на Managed Node. Для этого требуется запустить плейбук install_docker.yaml (Для установки Docker на Control Node, можно воспользоватся теми же коммандами, что и для Managed Node).
6) На Control Node создать prometheus.yaml - файл конфигурации (в данном примере он находится в SRE-lab/configs/)
7) Затем необходимо развернуть сбор метрик. Для этого требуется запустить плейбук deploy_metrics_collection.yaml

   В терминале должно быть что-то подобное:
   <img width="1202" height="619" alt="Image" src="https://github.com/user-attachments/assets/64210370-0f6b-4c7a-a887-b162005c5e71" />

8) В браузере необходимо открыть localhost:3000, после чего будет доступена страница логина Grafana:
   <img width="1164" height="977" alt="Image" src="https://github.com/user-attachments/assets/d6fa20b6-5e23-4d53-ac81-25d6ddd68e12" />
   
   При первом входе логин и пароль будут admin, admin. После первого логина, Grafana предложит поменять пароль.

9) Добавление источника данных. В верхней панели search надо ввести "Data sources":
   <img width="1272" height="183" alt="Image" src="https://github.com/user-attachments/assets/0eb7f498-1757-40f0-9006-80f1a2c34d5c" />

   Нажать "Add data source" и из предложенных вариантов выбрать Prometheus.
   <img width="1233" height="615" alt="Image" src="https://github.com/user-attachments/assets/e5f4b2d4-86f8-4a01-b444-2f4aea14d0c6" />

   Далее требуется заполнить настройки (имя и url):
   <img width="721" height="421" alt="Image" src="https://github.com/user-attachments/assets/f83fefea-a978-403d-bee6-f4ac1b286d08" />

   Затем нажать "Save and test" после чего должно появится сообщение "Successfully queried the Prometheus API".
   <img width="1241" height="259" alt="Image" src="https://github.com/user-attachments/assets/8dd9a5c8-41c5-444c-91b9-b2697153af3f" />
   
10) Далее надо добавить Dashboard. В левой панели выбрать Dashboard и нажать на "Create Dashboard":
   <img width="1224" height="562" alt="Image" src="https://github.com/user-attachments/assets/05a8f920-6185-4e1d-b37f-0d1f694d16f7" />

   Затем нажать на "Import Dashboard":
   <img width="981" height="518" alt="Image" src="https://github.com/user-attachments/assets/d870cdbc-e697-4334-b02a-543dea0ddff2" />

   В графе Find and import dashboards for common applications at grafana.com/dashboards ввести ID 1860 и нажать Load.
   <img width="621" height="328" alt="Image" src="https://github.com/user-attachments/assets/24e2b4c2-42aa-4780-9595-7ef51d5dcc95" />

11) В конечном итоге должна быть открыта страница с метриками CPU, RAM и т.д.
   <img width="1556" height="809" alt="Image" src="https://github.com/user-attachments/assets/2f72a7c9-a2c4-4c94-9826-4e320f267c0d" />