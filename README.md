# Домашнее задание к занятию 9 «Процессы CI/CD»

## Подготовка к выполнению

1. Создайте два VM в Yandex Cloud с параметрами: 2CPU 4RAM Centos7 (остальное по минимальным требованиям).

Через террформ создал 3 ВМ. Одна ансибл, и с нее настраиваются остальные.

2. Пропишите в [inventory](./infrastructure/inventory/cicd/hosts.yml) [playbook](./infrastructure/site.yml) созданные хосты.
3. Добавьте в [files](./infrastructure/files/) файл со своим публичным ключом (id_rsa.pub). Если ключ называется иначе — найдите таску в плейбуке, которая использует id_rsa.pub имя, и исправьте на своё.
4. Запустите playbook, ожидайте успешного завершения.

Исправил ошибки - не скачивалась 11 psql, заменил на 14 и скорректировал код что бы везде версия psql бралась бы из  переменной. Не везде это было в коде.
Плейбук отработал без ошибок:

![alt text](image.png)

![alt text](image-1.png)

5. Проверьте готовность SonarQube через [браузер](http://localhost:9000).

Готово.
![alt text](image-2.png)

6. Зайдите под admin\admin, поменяйте пароль на свой.

Сделал.

7.  Проверьте готовность Nexus через [бразуер](http://localhost:8081).

Готово.
![alt text](image-3.png)


8. Подключитесь под admin\admin123, поменяйте пароль, сохраните анонимный доступ.
Сделал.


## Знакомоство с SonarQube

### Основная часть

1. Создайте новый проект, название произвольное.

![alt text](image-4.png)

2. Скачайте пакет sonar-scanner, который вам предлагает скачать SonarQube.

Скачал:

3. Сделайте так, чтобы binary был доступен через вызов в shell (или поменяйте переменную PATH, или любой другой, удобный вам способ).

![alt text](image-5.png)

4. Проверьте `sonar-scanner --version`.

![alt text](image-6.png)

5. Запустите анализатор против кода из директории [example](./example) с дополнительным ключом `-Dsonar.coverage.exclusions=fail.py`.

```
root@ansible:~/SonarQube-Nexus-Maven/example# sonar-scanner \
  -Dsonar.projectKey=myproject \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://158.160.53.118:9000 \
  -Dsonar.login=1daec88195a9ab002bab5bae27da3fee0924d87f \
> -Dsonar.coverage.exclusions=fail.py
```

![alt text](image-7.png)



6. Посмотрите результат в интерфейсе.

![alt text](image-8.png)

![alt text](image-9.png)

7. Исправьте ошибки, которые он выявил, включая warnings.

![alt text](image-10.png)


8. Запустите анализатор повторно — проверьте, что QG пройдены успешно.

![alt text](image-11.png)

![alt text](image-12.png)

9. Сделайте скриншот успешного прохождения анализа, приложите к решению ДЗ.

## Знакомство с Nexus

### Основная часть

1. В репозиторий `maven-public` загрузите артефакт с GAV-параметрами:

 *    groupId: netology;
 *    artifactId: java;
 *    version: 8_282;
 *    classifier: distrib;
 *    type: tar.gz.
   
Создал пустой файл:
![alt text](image-13.png)

И загрузил:

![alt text](image-14.png)

2. В него же загрузите такой же артефакт, но с version: 8_102.

Еще раз загрузил только версию другую сделал.

3. Проверьте, что все файлы загрузились успешно.

![alt text](image-15.png)

4. В ответе пришлите файл `maven-metadata.xml` для этого артефекта.

```
<metadata modelVersion="1.1.0">
<groupId>netology</groupId>
<artifactId>java</artifactId>
<versioning>
<latest>8_282</latest>
<release>8_282</release>
<versions>
<version>8_102</version>
<version>8_282</version>
</versions>
<lastUpdated>20240514060218</lastUpdated>
</versioning>
</metadata>
```

![alt text](image-16.png)


### Знакомство с Maven


# 24/05/16 корректировки 

### Подготовка к выполнению

1. Скачайте дистрибутив с [maven](https://maven.apache.org/download.cgi).

```
wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.zip
unzip apache-maven-3.9.6-bin.zip -d mvn/
```

Зайти в папку bin и:

```
export PATH=$(pwd):$PATH
```

2. Разархивируйте, сделайте так, чтобы binary был доступен через вызов в shell (или поменяйте переменную PATH, или любой другой, удобный вам способ).

3. Удалите из `apache-maven-<version>/conf/settings.xml` упоминание о правиле, отвергающем HTTP- соединение — раздел mirrors —> id: my-repository-http-unblocker.


Нашел:

![alt text](image-18.png)

Удалил просто все строчки конфига где был прописан mirror.


4. Проверьте `mvn --version`.

![alt text](image-17.png)

5. Заберите директорию [mvn](./mvn) с pom.

В pom файле указал IP сервера nexus.
подтянул из гита весь код на сервер sonar-01 .

### Основная часть

1. Поменяйте в `pom.xml` блок с зависимостями под ваш артефакт из первого пункта задания для Nexus (java с версией 8_282).

Поменял так:

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>netology</groupId>
  <artifactId>simple-app</artifactId>
  <version>1.0-SNAPSHOT</version>
   <repositories>
    <repository>
      <id>my-repo</id>
      <name>maven-public</name>
      <url>http://158.160.108.198:8081/repository/maven-public/</url>
    </repository>
  </repositories>
  <dependencies>
    <dependency>
      <groupId>netology</groupId>
      <artifactId>java</artifactId>
      <version>8_282</version>
      <classifier>distrib</classifier>
      <type>tar.gz</type>
    </dependency>
  </dependencies>
</project>
```
Изменил групп и блок с зависимостями - подставил ip nexus.


2. Запустите команду `mvn package` в директории с `pom.xml`, ожидайте успешного окончания.

![alt text](image-24.png)

![alt text](image-25.png)

Зависимость успешно подтянулась, хоть она и пустая.

3. Проверьте директорию `~/.m2/repository/`, найдите ваш артефакт.

![alt text](image-26.png)

- подтянулось.

4. В ответе пришлите исправленный файл `pom.xml`.

см. выше и в репозитории git-hub.


---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---
