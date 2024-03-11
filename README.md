### Installation
```
cd ~/catkin_ws/src/
git clone https://github.com/voltbro/ws_serv_a.git 
```
### Compilation
```
cd ~/catkin_ws/
catkin_make --pkg ws_serv_a
```
### Usage

Just run configure.launch
```
roslaunch ws_serv_a start_configure_A.launch
```

##ssh
import os
import shutil
import paramiko

# Настройки
local_log_dir = '/path/to/local/logs'
archive_dir = '/path/to/local/archive'
remote_server = 'remote_server_ip'
remote_username = 'username'
remote_key_file = '/path/to/private_key'
remote_log_dir = '/home/userX'

# Архивация
for root, dirs, files in os.walk(local_log_dir):
    for dir in dirs:
        log_dir = os.path.join(root, dir)
        archive_name = dir + '.zip'
        shutil.make_archive(os.path.join(archive_dir, archive_name), 'zip', log_dir)

# Перемещение на удаленный сервер
ssh = paramiko.SSHClient()
ssh.load_system_host_keys()
ssh.connect(remote_server, username=remote_username, key_filename=remote_key_file)
sftp = ssh.open_sftp()
sftp.put(archive_dir, remote_log_dir)
sftp.close()
ssh.close()

# Удаление локальной папки archive
shutil.rmtree(archive_dir)

# Очистка папки на удаленном сервере
ssh = paramiko.SSHClient()
ssh.load_system_host_keys()
ssh.connect(remote_server, username=remote_username, key_filename=remote_key_file)
ssh.exec_command('rm -rf ' + remote_log_dir + '/*')
ssh.close()
