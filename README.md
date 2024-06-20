Hướng dẫn sủ dụng:

Bước1: Cài đặt ansible

$ sudo apt update

$ sudo apt install software-properties-common

$ sudo add-apt-repository --yes --update ppa:ansible/ansible

$ sudo apt install ansible

Tham khảo thêm cách cài tại: https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html

Bước2: Điều chỉnh file inventory
1. hosts.ini:

- File này là file lưu trữ tất cả các hostname và ip của của các node, nên trước khi chạy cần 
điều chỉnh cho đúng với trường hợp trước dựng cụm.

2. group_vars/all.yaml

- group_vars là đường dẫn chứa các file lưu trữ các biến chung của ansilbe mặc định.

- Trong file all.yaml cần điều chỉnh ansible_user là tên đăng nhập của các node
Lưu ý: Khi tạo máy build cụm lên đặt chung 1 tên đăng nhập để dễ quản lý

Bước3: Tạo public key ssh cho host điều khiển Ansible và copy public key sang các hosts muốn điều khiển
- Cài đặt ssh tạo public key cho ssh trên máy điều khiển bằng lệnh sau:

$ ssh-keygen -t rsa 4096

- Copy public key sang các hosts muốn điều khiển:

$ ansible-playbook -i inventory/hosts.ini SSH.yaml -K


Bước4: Ping thử các máy bằng ansible 

$ ansible -i inventory/hosts.ini -m ping all -K

Chú thích: 
- inventory/hosts.ini là đường dẫn lưu chữ host-ip
- K là sử dụng becompassword để nâng cao quyền, cho phép các tác vụ được thực hiện với quyền của người dùng khác, thường là root

Bước5: Tự động build cụm

$ ansible-playbook -i inventory/hosts.ini site.yaml -K





