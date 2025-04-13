
Cấu hình chuẩn bị build cụm khi dưới đây

1 host controllplane

| Name      |      IP      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  ctl1     | 192.168.x.x  |      6192     |      2       | 30GB(tối thiểu) |  Ubuntu 22-04 |

3 host worker

| Name      |      IP      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  mon1     | 192.168.x.x  |      2048     |      2       | 30GB(tối thiểu) |  Ubuntu 22-04 |
|  mon2     | 192.168.x.x  |      2048     |      2       | 30GB(tối thiểu) |  Ubuntu 22-04 |
|  mon3     | 192.168.x.x  |      2048     |      2       | 30GB(tối thiểu) |  Ubuntu 22-04 |

1 host remote để cài đặt ansible(Có thể có hoặc không)(*)

| Name      |      IP      |     Memory    |      Core    |      Disk       |      Os       |
|-----------|--------------|---------------|--------------|-----------------|---------------|
|  remote   | 192.168.x.x  |      2048     |      2       |      15GB       |  Ubuntu 22-04 |

1 host rancher(Có thể có hoặc không)(**)

|    Name      |      IP      |     Memory    |      Core    |      Disk       |      Os       |
|--------------|--------------|---------------|--------------|-----------------|---------------|
|  rancher     | 192.168.x.x  |      2048     |      2       |     20GB        |  Ubuntu 22-04 |


Lưu ý: 

    - (*) Host remote là để cài ansible, nếu bạn ko muốn tạo riêng 1 host remote thì có thể cài vào
    máy chính mà bạn muốn remote tuỳ chỉnh và đảm bảo rằng máy đó nó có thể ping tới các host khác trong mạng.
    
    - (**) Host rancher là để cài đặt Rancher là một công cụ để quản lý cụm k8s thông qua giao diện trực quan,
    host này có thể không cần cài nếu bạn ko muốn sử dụng giao diện UI quản lý các cụm tuỳ vào mục đích sử dụng 
    của bạn.

    - Địa chỉ IP của các host có thể cấu hình theo cách của bạn không nhất thiết phải giống cài đặt như trên.

Sau khi chuẩn bị cấu hình xong thì thực hiện các bước dưới đây để xây dựng cụm tự động

**Bước 1: Cài đặt ansible lên host remote**

    $ sudo apt update

    $ sudo apt install software-properties-common

    $ sudo add-apt-repository --yes --update ppa:ansible/ansible

    $ sudo apt install ansible

    Tham khảo thêm cách cài tại: https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html

**Bước 2: Điều chỉnh file inventory**

    1. hosts.ini:

    - File này là file lưu trữ tất cả các hostname và địa chỉ ip của của các host, nên trước khi 
    chạy cần điều chỉnh cho đúng địa chỉ ip và hostname trước khi chạy.

    2. group_vars/all.yaml

    - group_vars là đường dẫn chứa các file lưu trữ các biến chung của ansilbe mặc định.

    - Trong file all.yaml cần điều chỉnh các biến sau cho phù hợp: 

        ansible_user: là tên đăng nhập của các host

        ansible_password: là mật khẩu đăng nhập của các host

    Gợi ý: Khi tạo máy build cụm nên đặt chung tên đăng nhập và mật khẩu để dễ quản lý

**Bước 3: Tạo public key ssh cho host remote ansible và copy public key public sang các host muốn điều khiển**

    1. Cài đặt ssh và tạo public key ssh trên host remote Ansible bằng lệnh sau:
    
        $ sudo apt update

        $ sudo apt install openssh-server
        
        $ ssh-keygen -t rsa

    2. Cách copy public key từ host remote sang các host muốn điều khiển

    Trên máy remote chúng ta chuyển đến đường dẫn thư mục project được clone về máy.

    ví dụ:

        $ cd Ansible-K8s

    Sau đó chạy lệnh sau để copy public key từ host remote sang các host muốn điều khiển.

        $ ansible-playbook -i inventory/hosts.ini SSH.yaml -K


**Bước 4: Ping kiểm tra các hosts máy bằng ansible**

    $ ansible -i inventory/hosts.ini -m ping all -K

    Chú thích:  
    - Ping kiểm tra nếu các host trạng thái phản hồi là OK thì tiếp tục chạy bước 5, còn trạng thái phản hồi 
    là failed hoặc unreachable thì kiểm tra lại bước 2 và bước 3 điều chỉnh đúng thông số.

**Bước 5: Chạy lệnh tự động build cụm**

    $ ansible-playbook -i inventory/hosts.ini site.yaml -K





