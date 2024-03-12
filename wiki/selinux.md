# Fix samba shares permissions
sudo chcon -Rt samba_share_t /srv/container_data/
