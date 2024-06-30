# Домашнее задание к занятию «Основы Terraform. Yandex Cloud» - Филатов А. В.

### Цели задания

1. Создать свои ресурсы в облаке Yandex Cloud с помощью Terraform.
2. Освоить работу с переменными Terraform.


### Чек-лист готовности к домашнему заданию

1. Зарегистрирован аккаунт в Yandex Cloud. Использован промокод на грант.
2. Установлен инструмент Yandex CLI.
3. Исходный код для выполнения задания расположен в директории [**02/src**](https://github.com/netology-code/ter-homeworks/tree/main/02/src).


### Задание 0

1. Ознакомьтесь с [документацией к security-groups в Yandex Cloud](https://cloud.yandex.ru/docs/vpc/concepts/security-groups?from=int-console-help-center-or-nav). 
Этот функционал понадобится к следующей лекции.

------
### Внимание!! Обязательно предоставляем на проверку получившийся код в виде ссылки на ваш github-репозиторий!
------

### Задание 1
В качестве ответа всегда полностью прикладывайте ваш terraform-код в git.
Убедитесь что ваша версия **Terraform** ~>1.8.4

1. Изучите проект. В файле variables.tf объявлены переменные для Yandex provider.
2. Создайте сервисный аккаунт и ключ. [service_account_key_file](https://terraform-provider.yandexcloud.net).
4. Сгенерируйте новый или используйте свой текущий ssh-ключ. Запишите его открытую(public) часть в переменную **vms_ssh_public_root_key**.
5. Инициализируйте проект, выполните код. Исправьте намеренно допущенные синтаксические ошибки. Ищите внимательно, посимвольно. Ответьте, в чём заключается их суть.
6. Подключитесь к консоли ВМ через ssh и выполните команду ``` curl ifconfig.me```.
Примечание: К OS ubuntu "out of a box, те из коробки" необходимо подключаться под пользователем ubuntu: ```"ssh ubuntu@vm_ip_address"```. Предварительно убедитесь, что ваш ключ добавлен в ssh-агент: ```eval $(ssh-agent) && ssh-add``` Вы познакомитесь с тем как при создании ВМ создать своего пользователя в блоке metadata в следующей лекции.;
8. Ответьте, как в процессе обучения могут пригодиться параметры ```preemptible = true``` и ```core_fraction=5``` в параметрах ВМ.

В качестве решения приложите:

- скриншот ЛК Yandex Cloud с созданной ВМ, где видно внешний ip-адрес;
- скриншот консоли, curl должен отобразить тот же внешний ip-адрес;
- ответы на вопросы.

### Решение 1
![alt text](curl-ifconfig.png)
![alt text](console-vm.png)
- Ошибки в блоке resource "yandex_compute_instance" "platform":
* Неверное значение параметра platform_id и кол-во процессоров
- **`preemptible = true`**:
  - **Экономия затрат**: Прерываемые виртуальные машины стоят дешевле обычных, что позволяет значительно сократить расходы на вычислительные ресурсы в обучающих проектах.

- **`core_fraction = 5`**:
  - **Оптимизация ресурсов**: Указание небольшого значения для `core_fraction` (доля процессорного времени) позволяет запускать менее требовательные задачи, оптимизируя использование процессорных ресурсов и снижая затраты.




### Задание 2

1. Замените все хардкод-**значения** для ресурсов **yandex_compute_image** и **yandex_compute_instance** на **отдельные** переменные. К названиям переменных ВМ добавьте в начало префикс **vm_web_** .  Пример: **vm_web_name**.
2. Объявите нужные переменные в файле variables.tf, обязательно указывайте тип переменной. Заполните их **default** прежними значениями из main.tf. 
3. Проверьте terraform plan. Изменений быть не должно. 

### Решение 2

![alt text](main-add-var1.png)
![alt text](var-add.png)

### Задание 3

1. Создайте в корне проекта файл 'vms_platform.tf' . Перенесите в него все переменные первой ВМ.
2. Скопируйте блок ресурса и создайте с его помощью вторую ВМ в файле main.tf: **"netology-develop-platform-db"** ,  ```cores  = 2, memory = 2, core_fraction = 20```. Объявите её переменные с префиксом **vm_db_** в том же файле ('vms_platform.tf').  ВМ должна работать в зоне "ru-central1-b"
3. Примените изменения.

### Решение 3

![alt text](vm-db.png)
![alt text](var-db.png)
![alt text](console-2vm.png)

### Задание 4

1. Объявите в файле outputs.tf **один** output , содержащий: instance_name, external_ip, fqdn для каждой из ВМ в удобном лично для вас формате.(без хардкода!!!)
2. Примените изменения.

В качестве решения приложите вывод значений ip-адресов команды ```terraform output```.

### Решение 4

![alt text](outputs.png)
_ Outputs.tf _
```
output "web_instance_info" {
  value = {
    instance_name = yandex_compute_instance.platform.name
    external_ip   = yandex_compute_instance.platform.network_interface.0.nat_ip_address
    fqdn          = yandex_compute_instance.platform.fqdn
  }
}

output "db_instance_info" {
  value = {
    instance_name = yandex_compute_instance.db.name
    external_ip   = yandex_compute_instance.db.network_interface.0.nat_ip_address
    fqdn          = yandex_compute_instance.db.fqdn
  }
}

```

### Задание 5

1. В файле locals.tf опишите в **одном** local-блоке имя каждой ВМ, используйте интерполяцию ${..} с НЕСКОЛЬКИМИ переменными по примеру из лекции.
2. Замените переменные внутри ресурса ВМ на созданные вами local-переменные.
3. Примените изменения.

### Решение 5

![alt text](locals.png)
![alt text](add-locals1.png)


### Задание 6

1. Вместо использования трёх переменных  ".._cores",".._memory",".._core_fraction" в блоке  resources {...}, объедините их в единую map-переменную **vms_resources** и  внутри неё конфиги обеих ВМ в виде вложенного map.  
   ```
   пример из terraform.tfvars:
   vms_resources = {
     web={
       cores=
       memory=
       core_fraction=
       ...
     },
     db= {
       cores=
       memory=
       core_fraction=
       ...
     }
   }
   ```
3. Создайте и используйте отдельную map переменную для блока metadata, она должна быть общая для всех ваших ВМ.
   ```
   пример из terraform.tfvars:
   metadata = {
     serial-port-enable = 1
     ssh-keys           = "ubuntu:ssh-ed25519 AAAAC..."
   }
   ```  
  
5. Найдите и закоментируйте все, более не используемые переменные проекта.
6. Проверьте terraform plan. Изменений быть не должно.

### Решение 6

![alt text](vm-no-chng.png)

_ ```main.tf``` _
```
resource "yandex_vpc_network" "develop" {
  name = var.vpc_name
}
resource "yandex_vpc_subnet" "develop" {
  name           = "${var.vpc_name}-web"
  zone           = var.default_zone
  network_id     = yandex_vpc_network.develop.id
  v4_cidr_blocks = var.default_cidr
}

resource "yandex_vpc_subnet" "db" {
  name           = "${var.vpc_name}-db"
  zone           = var.vm_db_zone
  network_id     = yandex_vpc_network.develop.id
  v4_cidr_blocks = var.vm_db_cidr
}

data "yandex_compute_image" "ubuntu" {
  #family = "ubuntu-2004-lts"
  family = var.vm_web_image_family
}

# VM-Web
resource "yandex_compute_instance" "platform" {
  #name        = "netology-develop-platform-web"
  #name        = var.vm_web_name
  name        = local.web_instance_name
  #platform_id = "standart-v4"
  #platform_id = "standard-v2"
  platform_id = var.vm_web_platform_id
  resources {
  #  cores         = 1
  #  cores         = 1
  #  memory        = 1
  #  core_fraction = 5
    #cores         = var.vm_web_cores
    #memory        = var.vm_web_memory
    #core_fraction = var.vm_web_core_fraction
    cores           = var.vms_resources.vm_web.cores
    memory          = var.vms_resources.vm_web.memory
    core_fraction   = var.vms_resources.vm_web.core_fraction
    
  }
  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu.image_id
    }
  }
  scheduling_policy {
    #preemptible = true
    preemptible = var.vm_web_preemptible  
  }
  network_interface {
    subnet_id = yandex_vpc_subnet.develop.id
    #nat       = true
    nat       = var.vm_web_nat  
  }

  #metadata = {
  #  serial-port-enable = 1
  #  ssh-keys           = "ubuntu:${var.vms_ssh_root_key}"
  #}
  metadata = var.metadata

}

# VM-DB
resource "yandex_compute_instance" "db" {
  #name        = var.vm_db_name
  name        = local.db_instance_name
  zone        = var.vm_db_zone
  platform_id = var.vm_db_platform_id
  resources {
  #  cores         = var.vm_db_cores
  #  memory        = var.vm_db_memory
  #  core_fraction = var.vm_db_core_fraction
    cores           = var.vms_resources.vm_db.cores
    memory          = var.vms_resources.vm_db.memory
    core_fraction   = var.vms_resources.vm_db.core_fraction
  }
  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.ubuntu.image_id
    }
  }
  scheduling_policy {
    preemptible = var.vm_db_preemptible
  }
  network_interface {
    subnet_id = yandex_vpc_subnet.db.id
    nat       = var.vm_db_nat
  }
  #metadata = {
  #  serial-port-enable = "1"
  #  ssh-keys           = "ubuntu:${var.vms_ssh_root_key}"
  #}
  metadata = var.metadata

}

```
_ ```vms_platform.tf```_

```
#Metadata
variable   "metadata"  {
  default = {
     serial-port-enable = 1
     ssh-keys           = "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIB/f2jvClvaCqBITUrUdpyu6MyDyGzeL9/igTMIEUs24 corp\\ariota@IT"
   }
}

variable vms_resources {
  type = map
  default = {
    vm_web= {
      cores=2
      memory=1
      core_fraction=5
    },
    vm_db= {
      cores=2
      memory=2
      core_fraction=20
    }
  }
}

### Web
variable "vm_web_name" {
  type        = string
  default     = "netology-develop-platform-web"
}

variable "vm_web_platform_id" {
  type        = string
  default     = "standard-v2"
}

#variable "vm_web_cores" {
#  type        = number
#  default     = 2
#}

#variable "vm_web_memory" {
#  type        = number
#  default     = 1
#}

#variable "vm_web_core_fraction" {
#  type        = number
#  default     = 5
#}

variable "vm_web_preemptible" {
  type        = bool
  default     = true
}

variable "vm_web_nat" {
  type        = bool
  default     = true
}

variable "vm_web_image_family" {
  type        = string
  default     = "ubuntu-2004-lts"
}

#----------------------------------------------------------------------------

### DB
variable "vm_db_name" {
  type        = string
  default     = "netology-develop-platform-db"
}

variable "vm_db_platform_id" {
  type        = string
  default     = "standard-v1"
}

#variable "vm_db_cores" {
#  type        = number
#  default     = 2
#}

#variable "vm_db_memory" {
#  type        = number
#  default     = 2
#}

#variable "vm_db_core_fraction" {
#  type        = number
#  default     = 20
#}

variable "vm_db_preemptible" {
  type        = bool
  default     = true
}

variable "vm_db_nat" {
  type        = bool
  default     = true
}

variable "vm_db_image_family" {
  type        = string
  default     = "ubuntu-2004-lts"
}
```

------

## Дополнительное задание (со звёздочкой*)

**Настоятельно рекомендуем выполнять все задания со звёздочкой.**   
Они помогут глубже разобраться в материале. Задания со звёздочкой дополнительные, не обязательные к выполнению и никак не повлияют на получение вами зачёта по этому домашнему заданию. 


------
### Задание 7*

Изучите содержимое файла console.tf. Откройте terraform console, выполните следующие задания: 

1. Напишите, какой командой можно отобразить **второй** элемент списка test_list.
2. Найдите длину списка test_list с помощью функции length(<имя переменной>).
3. Напишите, какой командой можно отобразить значение ключа admin из map test_map.
4. Напишите interpolation-выражение, результатом которого будет: "John is admin for production server based on OS ubuntu-20-04 with X vcpu, Y ram and Z virtual disks", используйте данные из переменных test_list, test_map, servers и функцию length() для подстановки значений.

**Примечание**: если не догадаетесь как вычленить слово "admin", погуглите: "terraform get keys of map"

В качестве решения предоставьте необходимые команды и их вывод.

------

### Задание 8*
1. Напишите и проверьте переменную test и полное описание ее type в соответствии со значением из terraform.tfvars:
```
test = [
  {
    "dev1" = [
      "ssh -o 'StrictHostKeyChecking=no' ubuntu@62.84.124.117",
      "10.0.1.7",
    ]
  },
  {
    "dev2" = [
      "ssh -o 'StrictHostKeyChecking=no' ubuntu@84.252.140.88",
      "10.0.2.29",
    ]
  },
  {
    "prod1" = [
      "ssh -o 'StrictHostKeyChecking=no' ubuntu@51.250.2.101",
      "10.0.1.30",
    ]
  },
]
```
2. Напишите выражение в terraform console, которое позволит вычленить строку "ssh -o 'StrictHostKeyChecking=no' ubuntu@62.84.124.117"
------

------

### Задание 9*

Используя инструкцию https://cloud.yandex.ru/ru/docs/vpc/operations/create-nat-gateway#tf_1, настройте для ваших ВМ nat_gateway. Для проверки уберите внешний IP адрес (nat=false) у ваших ВМ и проверьте доступ в интернет с ВМ, подключившись к ней через serial console. Для подключения предварительно через ssh измените пароль пользователя: ```sudo passwd ubuntu```

### Правила приёма работыДля подключения предварительно через ssh измените пароль пользователя: sudo passwd ubuntu
В качестве результата прикрепите ссылку на MD файл с описанием выполненой работы в вашем репозитории. Так же в репозитории должен присутсвовать ваш финальный код проекта.

**Важно. Удалите все созданные ресурсы**.


### Критерии оценки

Зачёт ставится, если:

* выполнены все задания,
* ответы даны в развёрнутой форме,
* приложены соответствующие скриншоты и файлы проекта,
* в выполненных заданиях нет противоречий и нарушения логики.

На доработку работу отправят, если:

* задание выполнено частично или не выполнено вообще,
* в логике выполнения заданий есть противоречия и существенные недостатки. 
