Hướng dẫn sủ dụng:

Cấu hình chuẩn bị build cụm khi dưới đây

1 host remote để cài đặt ansible

| Name      |      Ip      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  remote   | 192.168.x.x  |      2048     |      2       |      15GB       |  Ubuntu 22-04 |

1 host controllplane

| Name      |      Ip      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  ctl1     | 192.168.x.x  |      6192     |      2       | 40GB(tối thiểu) |  Ubuntu 22-04 |

3 host worker

| Name      |      Ip      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  mon1     | 192.168.x.x  |      4096     |      2       | 40GB(tối thiểu) |  Ubuntu 22-04 |
|  mon2     | 192.168.x.x  |      4096     |      2       | 40GB(tối thiểu) |  Ubuntu 22-04 |
|  mon3     | 192.168.x.x  |      4096     |      2       | 40GB(tối thiểu) |  Ubuntu 22-04 |

1 host rancher

|    Name      |      Ip      |     Memory    |      Core    |      Disk       |      Os       |
|--------------|--------------|---------------|--------------|-----------------|---------------|
|  rancher     | 192.168.x.x  |      4096     |      2       |     30GB        |  Ubuntu 22-04 |

Chú thích: host rancher để cài riêng Rancher mục đích để quản lý cluster k8s thông qua giao diện trực quan.

Sau khi chuẩn bị cấu hình xong thì thực hiện các bước dưới đây để xây dựng cụm tự động

Bước1: Cài đặt ansible lên host remote

$ sudo apt update

$ sudo apt install software-properties-common

$ sudo add-apt-repository --yes --update ppa:ansible/ansible

$ sudo apt install ansible

Tham khảo thêm cách cài tại: https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html

Bước2: Điều chỉnh file inventory
1. hosts.ini:

- File này là file lưu trữ tất cả các hostname và địa chỉ ip của của các node, nên trước khi chạy cần 
điều chỉnh cho đúng địa chỉ ip và hosnamet trước khi chạy.

2. group_vars/all.yaml

- group_vars là đường dẫn chứa các file lưu trữ các biến chung của ansilbe mặc định.

- Trong file all.yaml cần điều chỉnh: 

ansible_user là tên đăng nhập của các node

ansible_password: là mật khẩu đăng nhập của các node

Khuyến cáo: Khi tạo máy build cụm nên đặt chung tên đăng nhập và mật khẩu để dễ quản lý

Bước3: Tạo public key ssh cho host romte ansible và copy public key public sang các hosts muốn điều khiển

1. Cài đặt ssh tạo public key cho ssh trên máy điều khiển bằng lệnh sau:

$ ssh-keygen -t rsa

2. Cách copy public key từ host remote sang các host muốn điều khiển

Trên máy remote chúng ta chuyển đến đường dẫn thư mục project được clone về máy.

ví dụ:

$ cd Ansible-K8s

Sau đó chạy lệnh sau để copy public key từ host remote sang các host muốn điều khiển.

$ ansible-playbook -i inventory/hosts.ini SSH.yaml -K


Bước4: Ping thử kiểm tra các hosts máy bằng ansible 

$ ansible -i inventory/hosts.ini -m ping all -K

Chú thích: 
- inventory/hosts.ini là đường dẫn lưu trữ host-ip
- K là sử dụng becompassword để nâng cao quyền, cho phép các tác vụ được thực hiện với quyền của người dùng khác, thường là root

Bước5: Chạy lệnh tự động build cụm

$ ansible-playbook -i inventory/hosts.ini site.yaml -K





