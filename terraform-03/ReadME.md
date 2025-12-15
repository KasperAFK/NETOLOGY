# ДЗ по теме "Управляющие конструкции в коде Terraform"

## Коментарий перед выполнением 

В момент выполнения задания у Яндекса были проблемы с зоной "ru-central1-a". По этому изменяю зону на "ru-central1-b".

И так же в подвешенном состаянии осталась сеть "develop". Сеть для данного ДЗ будет называться "develop_1".

![](./image/DZ_888_1.png)

## Задание 1

![](./image/DZ_888_2.png)

## Задание 2

count-vm.tf
```
resource "yandex_compute_instance" "web" {
  count = 2
  name  = "web-${count.index + 1}"

  platform_id = "standard-v1"

  resources {
    cores         = 2
    memory        = 1
    core_fraction = 5
  }

  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu.image_id
    }
  }

  scheduling_policy {
    preemptible = true
  }

  network_interface {
    subnet_id          = yandex_vpc_subnet.develop.id
    nat                = true
    security_group_ids = [yandex_vpc_security_group.example.id]
  }

  metadata = {
    serial-port-enable = 1
    ssh-keys           = "ubuntu:${local.ssh_key}"
  }

  depends_on = [yandex_compute_instance.db]
}
```

for_each-vm.tf
```
variable "each_vm" {
  type = list(object({
    vm_name     = string
    cpu         = number
    ram         = number
    disk_volume = number
  }))
  default = [
    {
      vm_name     = "main"
      cpu         = 4
      ram         = 4
      disk_volume = 20
    },
    {
      vm_name     = "replica"
      cpu         = 2
      ram         = 2
      disk_volume = 10
    }
  ]
}

resource "yandex_compute_instance" "db" {
  for_each = { for vm in var.each_vm : vm.vm_name => vm }

  name = each.value.vm_name

  platform_id = "standard-v1"

  resources {
    cores         = each.value.cpu
    memory        = each.value.ram
    core_fraction = 5
  }

  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu.image_id
      size     = each.value.disk_volume
    }
  }

  scheduling_policy {
    preemptible = true
  }

  network_interface {
    subnet_id          = yandex_vpc_subnet.develop.id
    nat                = true
    security_group_ids = [yandex_vpc_security_group.example.id]
  }

  metadata = {
    serial-port-enable = 1
    ssh-keys           = "ubuntu:${local.ssh_key}"
  }
}
```

locals.tf
```
locals {
  ssh_key = file("/home/kasper/.ssh/id_ed25519.pub")
}
```

data.tf
```
data "yandex_compute_image" "ubuntu" {
  family = "ubuntu-2004-lts"
}

```

kasper@ubuntu-666:~/DZ/ter-homeworks/03/src$ terraform apply
```
var.cloud_id
  https://cloud.yandex.ru/docs/resource-manager/operations/cloud/get-id

  Enter a value: b1gubbuupb71v3skn30c

var.folder_id
  https://cloud.yandex.ru/docs/resource-manager/operations/folder/get-id

  Enter a value: b1g0mnhpv5bo6smj2cj6

var.token
  OAuth-token; https://cloud.yandex.ru/docs/iam/concepts/authorization/oauth-token

  Enter a value: y0__xCUqJL4AhjB3RMgjrvJshTtHjM2mDLEnLw8yZJ-OQRMB9XX3g

data.yandex_compute_image.ubuntu: Reading...
yandex_vpc_network.develop: Refreshing state... [id=enpglcg6s81lfsj6emlc]
data.yandex_compute_image.ubuntu: Read complete after 0s [id=fd8e4du4rapbt83p6c7i]
yandex_vpc_security_group.example: Refreshing state... [id=enpnt11ofkv55fs81s7p]
yandex_vpc_subnet.develop: Refreshing state... [id=e2lgq3r9mv83u53piho9]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # yandex_compute_instance.db["main"] will be created
  + resource "yandex_compute_instance" "db" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + gpu_cluster_id            = (known after apply)
      + hardware_generation       = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + maintenance_grace_period  = (known after apply)
      + maintenance_policy        = (known after apply)
      + metadata                  = {
          + "serial-port-enable" = "1"
          + "ssh-keys"           = <<-EOT
                ubuntu:ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILSX7L9kEfKj4GeYrmzBnRnIAsjcGJ8J+eG7yd44cFjs kasper@ubuntu-666
            EOT
        }
      + name                      = "main"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + status                    = (known after apply)
      + zone                      = (known after apply)

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8e4du4rapbt83p6c7i"
              + name        = (known after apply)
              + size        = 20
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + metadata_options (known after apply)

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = [
              + "enpnt11ofkv55fs81s7p",
            ]
          + subnet_id          = "e2lgq3r9mv83u53piho9"
        }

      + placement_policy (known after apply)

      + resources {
          + core_fraction = 5
          + cores         = 4
          + memory        = 4
        }

      + scheduling_policy {
          + preemptible = true
        }
    }

  # yandex_compute_instance.db["replica"] will be created
  + resource "yandex_compute_instance" "db" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + gpu_cluster_id            = (known after apply)
      + hardware_generation       = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + maintenance_grace_period  = (known after apply)
      + maintenance_policy        = (known after apply)
      + metadata                  = {
          + "serial-port-enable" = "1"
          + "ssh-keys"           = <<-EOT
                ubuntu:ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILSX7L9kEfKj4GeYrmzBnRnIAsjcGJ8J+eG7yd44cFjs kasper@ubuntu-666
            EOT
        }
      + name                      = "replica"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + status                    = (known after apply)
      + zone                      = (known after apply)

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8e4du4rapbt83p6c7i"
              + name        = (known after apply)
              + size        = 10
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + metadata_options (known after apply)

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = [
              + "enpnt11ofkv55fs81s7p",
            ]
          + subnet_id          = "e2lgq3r9mv83u53piho9"
        }

      + placement_policy (known after apply)

      + resources {
          + core_fraction = 5
          + cores         = 2
          + memory        = 2
        }

      + scheduling_policy {
          + preemptible = true
        }
    }

  # yandex_compute_instance.web[0] will be created
  + resource "yandex_compute_instance" "web" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + gpu_cluster_id            = (known after apply)
      + hardware_generation       = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + maintenance_grace_period  = (known after apply)
      + maintenance_policy        = (known after apply)
      + metadata                  = {
          + "serial-port-enable" = "1"
          + "ssh-keys"           = <<-EOT
                ubuntu:ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILSX7L9kEfKj4GeYrmzBnRnIAsjcGJ8J+eG7yd44cFjs kasper@ubuntu-666
            EOT
        }
      + name                      = "web-1"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + status                    = (known after apply)
      + zone                      = (known after apply)

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8e4du4rapbt83p6c7i"
              + name        = (known after apply)
              + size        = (known after apply)
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + metadata_options (known after apply)

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = [
              + "enpnt11ofkv55fs81s7p",
            ]
          + subnet_id          = "e2lgq3r9mv83u53piho9"
        }

      + placement_policy (known after apply)

      + resources {
          + core_fraction = 5
          + cores         = 2
          + memory        = 1
        }

      + scheduling_policy {
          + preemptible = true
        }
    }

  # yandex_compute_instance.web[1] will be created
  + resource "yandex_compute_instance" "web" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + gpu_cluster_id            = (known after apply)
      + hardware_generation       = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + maintenance_grace_period  = (known after apply)
      + maintenance_policy        = (known after apply)
      + metadata                  = {
          + "serial-port-enable" = "1"
          + "ssh-keys"           = <<-EOT
                ubuntu:ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILSX7L9kEfKj4GeYrmzBnRnIAsjcGJ8J+eG7yd44cFjs kasper@ubuntu-666
            EOT
        }
      + name                      = "web-2"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + status                    = (known after apply)
      + zone                      = (known after apply)

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8e4du4rapbt83p6c7i"
              + name        = (known after apply)
              + size        = (known after apply)
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + metadata_options (known after apply)

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = [
              + "enpnt11ofkv55fs81s7p",
            ]
          + subnet_id          = "e2lgq3r9mv83u53piho9"
        }

      + placement_policy (known after apply)

      + resources {
          + core_fraction = 5
          + cores         = 2
          + memory        = 1
        }

      + scheduling_policy {
          + preemptible = true
        }
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

yandex_compute_instance.db["replica"]: Creating...
yandex_compute_instance.db["main"]: Creating...
yandex_compute_instance.db["replica"]: Still creating... [00m10s elapsed]
yandex_compute_instance.db["main"]: Still creating... [00m10s elapsed]
yandex_compute_instance.db["replica"]: Still creating... [00m20s elapsed]
yandex_compute_instance.db["main"]: Still creating... [00m20s elapsed]
yandex_compute_instance.db["replica"]: Still creating... [00m30s elapsed]
yandex_compute_instance.db["main"]: Still creating... [00m30s elapsed]
yandex_compute_instance.db["replica"]: Still creating... [00m40s elapsed]
yandex_compute_instance.db["main"]: Still creating... [00m40s elapsed]
yandex_compute_instance.db["main"]: Creation complete after 43s [id=epd871rialbkibmlre4m]
yandex_compute_instance.db["replica"]: Creation complete after 45s [id=epd7colc7kq79n19gfpt]
yandex_compute_instance.web[0]: Creating...
yandex_compute_instance.web[1]: Creating...
yandex_compute_instance.web[0]: Still creating... [00m10s elapsed]
yandex_compute_instance.web[1]: Still creating... [00m10s elapsed]
yandex_compute_instance.web[0]: Still creating... [00m20s elapsed]
yandex_compute_instance.web[1]: Still creating... [00m20s elapsed]
yandex_compute_instance.web[0]: Still creating... [00m30s elapsed]
yandex_compute_instance.web[1]: Still creating... [00m30s elapsed]
yandex_compute_instance.web[1]: Creation complete after 31s [id=epd4p51n0aj8hv0ledfj]
yandex_compute_instance.web[0]: Still creating... [00m40s elapsed]
yandex_compute_instance.web[0]: Creation complete after 41s [id=epdkqqq6ge1sgvrs6nvh]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
```

## Задание 3

disk_vm.tf
```
resource "yandex_compute_disk" "storage_disk" {
  count = 3
  name  = "storage-disk-${count.index + 1}"
  type  = "network-hdd"
  zone  = var.default_zone
  size  = 1
}

resource "yandex_compute_instance" "storage" {
  name = "storage"

  platform_id = "standard-v1"

  resources {
    cores         = 2
    memory        = 1
    core_fraction = 5
  }

  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu.image_id
    }
  }

  dynamic "secondary_disk" {
    for_each = yandex_compute_disk.storage_disk
    content {
      disk_id = secondary_disk.value.id
    }
  }

  scheduling_policy {
    preemptible = true
  }

  network_interface {
    subnet_id          = yandex_vpc_subnet.develop.id
    nat                = true
    security_group_ids = [yandex_vpc_security_group.example.id]
  }

  metadata = {
    serial-port-enable = 1
    ssh-keys           = "ubuntu:${local.ssh_key}"
  }
}
```

kasper@ubuntu-666:~/DZ/ter-homeworks/03/src$ terraform apply
```
var.cloud_id
  https://cloud.yandex.ru/docs/resource-manager/operations/cloud/get-id

  Enter a value: b1gubbuupb71v3skn30c

var.folder_id
  https://cloud.yandex.ru/docs/resource-manager/operations/folder/get-id

  Enter a value: b1g0mnhpv5bo6smj2cj6

var.token
  OAuth-token; https://cloud.yandex.ru/docs/iam/concepts/authorization/oauth-token

  Enter a value: y0__xCUqJL4AhjB3RMgjrvJshTtHjM2mDLEnLw8yZJ-OQRMB9XX3g

data.yandex_compute_image.ubuntu: Reading...
yandex_vpc_network.develop: Refreshing state... [id=enpglcg6s81lfsj6emlc]
data.yandex_compute_image.ubuntu: Read complete after 0s [id=fd8e4du4rapbt83p6c7i]
yandex_vpc_security_group.example: Refreshing state... [id=enpnt11ofkv55fs81s7p]
yandex_vpc_subnet.develop: Refreshing state... [id=e2lgq3r9mv83u53piho9]
yandex_compute_instance.db["replica"]: Refreshing state... [id=epd7colc7kq79n19gfpt]
yandex_compute_instance.db["main"]: Refreshing state... [id=epd871rialbkibmlre4m]
yandex_compute_instance.web[0]: Refreshing state... [id=epdkqqq6ge1sgvrs6nvh]
yandex_compute_instance.web[1]: Refreshing state... [id=epd4p51n0aj8hv0ledfj]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # yandex_compute_disk.storage_disk[0] will be created
  + resource "yandex_compute_disk" "storage_disk" {
      + block_size  = 4096
      + created_at  = (known after apply)
      + folder_id   = (known after apply)
      + id          = (known after apply)
      + name        = "storage-disk-1"
      + product_ids = (known after apply)
      + size        = 1
      + status      = (known after apply)
      + type        = "network-hdd"
      + zone        = "ru-central1-b"

      + disk_placement_policy (known after apply)

      + hardware_generation (known after apply)
    }

  # yandex_compute_disk.storage_disk[1] will be created
  + resource "yandex_compute_disk" "storage_disk" {
      + block_size  = 4096
      + created_at  = (known after apply)
      + folder_id   = (known after apply)
      + id          = (known after apply)
      + name        = "storage-disk-2"
      + product_ids = (known after apply)
      + size        = 1
      + status      = (known after apply)
      + type        = "network-hdd"
      + zone        = "ru-central1-b"

      + disk_placement_policy (known after apply)

      + hardware_generation (known after apply)
    }

  # yandex_compute_disk.storage_disk[2] will be created
  + resource "yandex_compute_disk" "storage_disk" {
      + block_size  = 4096
      + created_at  = (known after apply)
      + folder_id   = (known after apply)
      + id          = (known after apply)
      + name        = "storage-disk-3"
      + product_ids = (known after apply)
      + size        = 1
      + status      = (known after apply)
      + type        = "network-hdd"
      + zone        = "ru-central1-b"

      + disk_placement_policy (known after apply)

      + hardware_generation (known after apply)
    }

  # yandex_compute_instance.storage will be created
  + resource "yandex_compute_instance" "storage" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + gpu_cluster_id            = (known after apply)
      + hardware_generation       = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + maintenance_grace_period  = (known after apply)
      + maintenance_policy        = (known after apply)
      + metadata                  = {
          + "serial-port-enable" = "1"
          + "ssh-keys"           = <<-EOT
                ubuntu:ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILSX7L9kEfKj4GeYrmzBnRnIAsjcGJ8J+eG7yd44cFjs kasper@ubuntu-666
            EOT
        }
      + name                      = "storage"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + status                    = (known after apply)
      + zone                      = (known after apply)

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8e4du4rapbt83p6c7i"
              + name        = (known after apply)
              + size        = (known after apply)
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + metadata_options (known after apply)

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = [
              + "enpnt11ofkv55fs81s7p",
            ]
          + subnet_id          = "e2lgq3r9mv83u53piho9"
        }

      + placement_policy (known after apply)

      + resources {
          + core_fraction = 5
          + cores         = 2
          + memory        = 1
        }

      + scheduling_policy {
          + preemptible = true
        }

      + secondary_disk {
          + auto_delete = false
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = "READ_WRITE"
        }
      + secondary_disk {
          + auto_delete = false
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = "READ_WRITE"
        }
      + secondary_disk {
          + auto_delete = false
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = "READ_WRITE"
        }
    }

Plan: 4 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

yandex_compute_disk.storage_disk[1]: Creating...
yandex_compute_disk.storage_disk[2]: Creating...
yandex_compute_disk.storage_disk[0]: Creating...
yandex_compute_disk.storage_disk[0]: Creation complete after 7s [id=epdet67a86b6csfol56h]
yandex_compute_disk.storage_disk[1]: Still creating... [00m10s elapsed]
yandex_compute_disk.storage_disk[2]: Still creating... [00m10s elapsed]
yandex_compute_disk.storage_disk[2]: Creation complete after 12s [id=epdg2khcb0ao1kecekug]
yandex_compute_disk.storage_disk[1]: Creation complete after 12s [id=epdnj5150njrshcbgrij]
yandex_compute_instance.storage: Creating...
yandex_compute_instance.storage: Still creating... [00m10s elapsed]
yandex_compute_instance.storage: Still creating... [00m20s elapsed]
yandex_compute_instance.storage: Still creating... [00m30s elapsed]
yandex_compute_instance.storage: Creation complete after 36s [id=epdo6hqprkhvhuunkb9u]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
```

## Задание 4

ansible.tf 
```
resource "local_file" "inventory_cfg" {
  content = templatefile("${path.module}/hosts.tftpl", {
    webservers = yandex_compute_instance.web
    databases  = yandex_compute_instance.db
    storage    = [yandex_compute_instance.storage]
  })

  filename = "${abspath(path.module)}/hosts.cfg"
}
```

hosts.tftpl
```
[webservers]
%{~ for vm in webservers ~}
${vm.name} ansible_host=${vm.network_interface[0].nat_ip_address} fqdn=${vm.fqdn}
%{~ endfor ~}

[databases]
%{~ for vm in databases ~}
${vm.name} ansible_host=${vm.network_interface[0].nat_ip_address} fqdn=${vm.fqdn}
%{~ endfor ~}

[storage]
%{~ for vm in storage ~}
${vm.name} ansible_host=${vm.network_interface[0].nat_ip_address} fqdn=${vm.fqdn}
%{~ endfor ~}
```

kasper@ubuntu-666:~/DZ/ter-homeworks/03/src$ terraform apply
```
var.cloud_id
  https://cloud.yandex.ru/docs/resource-manager/operations/cloud/get-id

  Enter a value: b1gubbuupb71v3skn30c

var.folder_id
  https://cloud.yandex.ru/docs/resource-manager/operations/folder/get-id

  Enter a value: b1g0mnhpv5bo6smj2cj6

var.token
  OAuth-token; https://cloud.yandex.ru/docs/iam/concepts/authorization/oauth-token

  Enter a value: y0__xCUqJL4AhjB3RMgjrvJshTtHjM2mDLEnLw8yZJ-OQRMB9XX3g

yandex_compute_disk.storage_disk[0]: Refreshing state... [id=epdet67a86b6csfol56h]
yandex_vpc_network.develop: Refreshing state... [id=enpglcg6s81lfsj6emlc]
yandex_compute_disk.storage_disk[1]: Refreshing state... [id=epdnj5150njrshcbgrij]
yandex_compute_disk.storage_disk[2]: Refreshing state... [id=epdg2khcb0ao1kecekug]
data.yandex_compute_image.ubuntu: Reading...
data.yandex_compute_image.ubuntu: Read complete after 0s [id=fd8e4du4rapbt83p6c7i]
yandex_vpc_security_group.example: Refreshing state... [id=enpnt11ofkv55fs81s7p]
yandex_vpc_subnet.develop: Refreshing state... [id=e2lgq3r9mv83u53piho9]
yandex_compute_instance.db["replica"]: Refreshing state... [id=epd7colc7kq79n19gfpt]
yandex_compute_instance.storage: Refreshing state... [id=epdo6hqprkhvhuunkb9u]
yandex_compute_instance.db["main"]: Refreshing state... [id=epd871rialbkibmlre4m]
yandex_compute_instance.web[0]: Refreshing state... [id=epdkqqq6ge1sgvrs6nvh]
yandex_compute_instance.web[1]: Refreshing state... [id=epd4p51n0aj8hv0ledfj]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply" which may have affected this plan:

  # yandex_compute_instance.storage has changed
  ~ resource "yandex_compute_instance" "storage" {
        id                        = "epdo6hqprkhvhuunkb9u"
      + labels                    = {}
        name                      = "storage"
        # (14 unchanged attributes hidden)

        # (9 unchanged blocks hidden)
    }


Unless you have made equivalent changes to your configuration, or ignored the relevant attributes using ignore_changes, the following plan may include actions to undo or
respond to these changes.

────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # local_file.inventory_cfg will be created
  + resource "local_file" "inventory_cfg" {
      + content              = <<-EOT
            [webservers]web-1 ansible_host=158.160.29.212 fqdn=epdkqqq6ge1sgvrs6nvh.auto.internalweb-2 ansible_host=51.250.102.109 fqdn=epd4p51n0aj8hv0ledfj.auto.internal
            [databases]main ansible_host=89.169.183.82 fqdn=epd871rialbkibmlre4m.auto.internalreplica ansible_host=158.160.24.157 fqdn=epd7colc7kq79n19gfpt.auto.internal
            [storage]storage ansible_host=158.160.95.204 fqdn=epdo6hqprkhvhuunkb9u.auto.internal
        EOT
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "/home/kasper/DZ/ter-homeworks/03/src/hosts.cfg"
      + id                   = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

local_file.inventory_cfg: Creating...
local_file.inventory_cfg: Creation complete after 0s [id=144503194a7f2a4f03643270b0f3eff924753904]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

![](./image/DZ_888_3.png)
