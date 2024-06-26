
Cấu hình chuẩn bị build cụm khi dưới đây

1 host remote để cài đặt ansible

| Name      |      Ip      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  remote   | 192.168.x.x  |      2048     |      2       |      15GB       |  Ubuntu 22-04 |

1 host controllplane

| Name      |      Ip      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  ctl1     | 192.168.x.x  |      6192     |      2       | 30GB(tối thiểu) |  Ubuntu 22-04 |

3 host worker

| Name      |      Ip      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  mon1     | 192.168.x.x  |      2048     |      2       | 30GB(tối thiểu) |  Ubuntu 22-04 |
|  mon2     | 192.168.x.x  |      2048     |      2       | 30GB(tối thiểu) |  Ubuntu 22-04 |
|  mon3     | 192.168.x.x  |      2048     |      2       | 30GB(tối thiểu) |  Ubuntu 22-04 |

1 host rancher(Rancher quản lý cụm thông qua giao diện trực quan)

|    Name      |      Ip      |     Memory    |      Core    |      Disk       |      Os       |
|--------------|--------------|---------------|--------------|-----------------|---------------|
|  rancher     | 192.168.x.x  |      2048     |      2       |     20GB        |  Ubuntu 22-04 |


Lưu ý: 

    - Địa chỉ IP có thể cấu hình theo cách của bạn không nhất thiết phải giống cài đặt như trên.
    
    - Bạn có thể cài rancher lên host controll plane nhưng chú ý Rancher tiêu hao 1 phần ko nhỏ 
    tài nguyên của hệ thống lên khuyến khích bạn tách tiêng ra 1 host riêng để dễ quản lý và tránh 
    tiêu hao tài nguyên của host control plane gây mất ổn định hệ thống.

Sau khi chuẩn bị cấu hình xong thì thực hiện các bước dưới đây để xây dựng cụm tự động

**Bước1: Cài đặt ansible lên host remote**

    $ sudo apt update

    $ sudo apt install software-properties-common

    $ sudo add-apt-repository --yes --update ppa:ansible/ansible

    $ sudo apt install ansible

    Tham khảo thêm cách cài tại: https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html

**Bước2: Điều chỉnh file inventory**

    1. hosts.ini:

    - File này là file lưu trữ tất cả các hostname và địa chỉ ip của của các host, nên trước khi 
    chạy cần điều chỉnh cho đúng địa chỉ ip và hostname trước khi chạy.

    2. group_vars/all.yaml

    - group_vars là đường dẫn chứa các file lưu trữ các biến chung của ansilbe mặc định.

    - Trong file all.yaml cần điều chỉnh các biến sau cho phù hợp: 

        ansible_user: là tên đăng nhập của các host

        ansible_password: là mật khẩu đăng nhập của các host

    Gợi ý: Khi tạo máy build cụm nên đặt chung tên đăng nhập và mật khẩu để dễ quản lý

**Bước3: Tạo public key ssh cho host remote ansible và copy public key public sang các host muốn điều khiển**

    1. Cài đặt ssh tạo public key cho ssh trên máy điều khiển bằng lệnh sau:
        $ sudo apt update

        $ sudo apt install openssh-server
        
        $ ssh-keygen -t rsa

    2. Cách copy public key từ host remote sang các host muốn điều khiển

    Trên máy remote chúng ta chuyển đến đường dẫn thư mục project được clone về máy.

    ví dụ:

        $ cd Ansible-K8s

    Sau đó chạy lệnh sau để copy public key từ host remote sang các host muốn điều khiển.

        $ ansible-playbook -i inventory/hosts.ini SSH.yaml -K


**Bước4: Ping kiểm tra các hosts máy bằng ansible**

    $ ansible -i inventory/hosts.ini -m ping all -K

    Chú thích: 
        
    - Ping kiểm tra nếu các host trạng thái phản hồi là OK thì tiếp tục chạy bước 5, 
    còn trạng thái phản hồi là failed hoặc unreachable thì kiểm tra lại bước 2 và bước 
    3 điều chỉnh đúng thông số yêu cầu.

**Bước5: Chạy lệnh tự động build cụm**

    $ ansible-playbook -i inventory/hosts.ini site.yaml -K





