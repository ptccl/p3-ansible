# p3-ansible

**Membuat Inventory**
Di sini kita membuat nama komputer menjadi host01. Agar ansible dapat mengetahui, kita menempatkan hostname kita (host01) ke *Inventory File*. Kita beri nama inventory file dengan nama myhosts, dan grup yang diberi nama group1.
Tuliskan sintak di bawah ini:

```echo "[group1]" > myhosts```

Sintak di atas akan memasukkan group1 ke dalam inventory file myhosts

Kemudian, tambahkan host ke grup dengan sintak:

```echo "host01 ansible_ssh_user=ubuntu" >> myhosts```

Di sini kita juga mengatur username untuk servis SSH, yaitu ubuntu.

**Menjalankan Ansible**
-Ad-hoc Tasks
Untuk menjalankan ansible secara ad-hoc, menggunakan sebuah modul, menggunakan command *ansible*.
Contohnya, untuk mengecek tanggal dan waktu saat ini dari remote host:

```ansible group1 -i myhosts -m command -a date```

-Playbook
Playbook ini merupakan cara lain untuk menjalankan ansible.
Menjalankan playbook
Playbook ini akan menjalankan sebuah task pada host01.
This playbook runs one task, on our one host, host01. Perhatikan indentation (-), ini sangat penting untuk menentukan bagaimana file diparsing. Garis kosong akan dikecualikan, tetapi akan membuat playbook lebih mudah dibaca untuk manusia.
Kita buka site.yml, dengan sintak:

```cat site.yml```

Maka isinya seperti di bawah ini:

```
---
- hosts: host01

  become: true

  tasks:
    - name: ensure latest sysstat is installed
      apt:
        name: sysstat
        state: latest
```

Untuk menjalankan playbook, gunakan command ansible-playbookdengan inventory file myhosts kita tadi.

```ansible-playbook -i myhosts site.yml```

Ansible sebaiknya mengembalikan hasil *Changed=1*, mengindikasikan bahwa package telah terinstall.

**Memastikan package sudah terhapus**
Contoh mengganti versi sysstat dari latest menjadi versi 10.2.0-1
Pada site.yml diganti menjadi seperti di bawah ini:

```
- name: ensure sysstat is installed at version 10.2.0-1
  apt:
    name: sysstat=10.2.0-1
    state: installed
```

Jika ingin memastikan bahwa package tidak diinstall, bisa ditambahkan command *absent*.

Update playbook untuk menghapus sysstat, dengan sintak:

```sed -i -e 's/state: latest/state: absent/' -e 's/ensure.*/ensure sysstat is removed/' site.yml```

Kemudian jalankan kembali playbook, dengan sintak:

```ansible-playbook -i myhosts site.yml```

Pada intinya, playbook yang sama sebaiknya selalu bisa untuk menjalankan pada sebuah host dan diasumsikan tidak ada error.
Contohnya, pada saat playbook dijalankan dan ternyata mati di tengah jalan karena masalah jaringan, ini akan tetap berhasil ketika kita menjalankannya ulang.
