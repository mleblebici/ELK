`[İçindekiler]`
### ELK Stack Nedir?
#### Elasticsearch
#### Logstash
#### Kibana
### Çalışma Prensipleri
#### Elasticsearch
##### Terminoloji
##### Kurulum
##### Konfigürasyon
##### Elasticsearch REST API
##### Örnek Kullanım
#### Logstash
##### Kurulum
##### Konfigürasyon
##### Pluginler
###### Input Pluginleri
###### Output Pluginleri
###### Filter Pluginleri
#### Kibana
##### Kurulum
##### Görselleştirme
##### Diğer Ayarlar
### ELK Stack Kullanım Senaryoları
#### ELK ile Bro IDS Loglarının Yönetimi
#### ELK ile Syslog Loglarının Yönetimi
#### ELK ile Suricata Loglarının Yönetimi
#### ELK ile pfSense Loglarının Yönetimi

------------------------------------

# ELK Stack Nedir?

ELK Stack üç tane açık kaynak kodlu ürünün (Elasticsearch, Logstash ve Kibana) birleşimidir. ELK Stack dünyanın en popüler log yönetim programlarından birisidir. ELK Stack iş zekası, güvenlik, uyum ve web analitiği alanlarında en çok kullanılan araçlardan birisidir. Logstash logları toplar ve ayrıştırır, sonrasında Elasticsearch bu logları endeksler ve saklar. Kibana ise görsel bir şekilde bu logları kullanıcılara sunar.

## Elasticsearch
Elasticsearch çoğu zaman bir arama sunucusu olarak da anılmaktadır. Normalde insanlar arama işlevini kendilerinin yaptıkları basit bir işlem olarak görürler. Oysa ki arama işlemi çok karmaşık bir işlem olup bizlere hizmet olarak sunulmaktadır. Elasticsearch bu hizmeti sunan arama sunucularından bir tanesidir. Fakat bildiğimiz SQL veritabanlarından farklı olarak veriyi belirli bir düzen içerisinde tutmaz. Elasticsearch verinin ne kadar düzenli tutulduğundan çok aramalara karşı ne kadar iyi performansa sahip olduğuna önem verir.

## Logstash
Logstash ELK Stack’in tam ortasında bulunan olmazsa olmaz parçalardan biridir. Elasticsearch verileri ne kadar iyi endekslerse endekslesin, bu verilerin öncelikle bir yerden ELK sunucusuna gelmesi gerekmektedir. Çeşitli kaynaklardan bu verilerin gelmesi işlemini Logstash gerçekleştirmektedir. Yani kısaca Logstash çeşitli uygulama ve sunuculardan logları alıp Elasticsearch e saklaması için iletmektedir.

Bunlardan farklı olarak Logstash ayrıca veriyi filtreleme ve şekillendirme görevini de yerine getirmektedir. Bu sayede gelen veriler anlamlandırılmakta ve kullanıcının kolayca anlayabileceği bir hale gelmektedir.

## Kibana
Her sistemde olduğu gibi ELK Stack için de görsellik çok önemlidir. Kibana ELK Stack’de tutulan logların görselleştirilmesi görevini üstlenmektedir. Kibana ile loglar kategorilere göre ayırılabilmekte, belirli kriterlere göre daraltılabilmekte ve istenilen formatta görsel grafikler şeklinde kullanıcıya gösterilebilmektedir.

# Çalışma Prensipleri
## Elasticsearch
### Terminoloji
Elasticsearch arama işlemini kolaylaştırmak ve verileri düzenli bir şekilde tutabilmek için farklı bir mimari yapı kullanmaktadır. Bu yapıda en üstte dizi (cluster) gelmektedir. Dizi bir veya daha fazla düğümün (node) birleşmesinden oluşan ve tüm bu düğümler arasında endeksleme ve arama yetenekleri sağlanan birimdir. Düğüm ise verileri saklayan tek bir sunucudur. Bir düğüm istenilen diziye ait olacak şekilde ayarlanabilmektedir. Endeks (index) benzer özellikler içeren dökümanların birleşiminden oluşmaktadır. Farklı veri kategorileri için farklı endeksler oluşturabilmek mümkündür. Tip (type) benzer yapı içeren verilere verilen genel bir yapıdır. Döküman (document) ise endekslenebilen en temel bilgi birimidir.

Bir endeksteki verilerin boyutu çok büyük olabileceği için, donanımsal problemler yaşamamak adına endeksteki veriler zarflara (shards) ayırılmaktadır. Bu sayede içerik hacmi yatay olarak ayrılırken aynı zamanda bir arama işlemi zarflar üzerinde paralel olarak gerçekleştirilebilmektedir. Sistemde beklenmeyen problemlerin yaşanması durumlarda veri kaybı yaşamamak için bu zarfların kopyaları (replicas) da ayrıca tutulabilmektedir. Bu kopyaların sayısı her bir endeks için kullanıcılar tarafından ayarlanabilmektedir.

### Kurulum
Bu çalışma kapsamında anlatılan bütün kurulumlar CentOS 7 üzerinde yapılacaktır. Ayrıca makine kurulurken ‘Server with GUI’ seçeneği seçilmelidir. Bu seçeneğin seçilmesi gerekli olan pek çok kütüphane ve uygulamanın yüklü olarak gelmesini sağlayacaktır (örn. Java, EPEL repository). 
* Öncelikle GPG imza anahtarını indirip yükleyiniz:
```sh
$ rpm –import https://packages.elastic.co/GPG-KEY-elasticsearch
```

* Elasticsearch’ün yüklenebilmesi için gerekli olan repo dosyasını aşağıdaki gibi oluşturunuz ve elasticsearch.repo olarak /etc/yum.repos.d klasörü altına kaydediniz:
![alt text](1.png)

* Oluşturduğunuz repo dosyasını kullanarak elasticsearch’ü yükleyiniz:
```sh
$ yum -y install elasticsearch
```

* Elasticsearch’ün sistem açılışında servis olarak çalışması için aşağıdaki komutları çalıştırınız:
```sh
$ systemctl daemon-reload
$ systemctl enable elasticsearch.service
$ systemctl start elasticsearch.service
```

* Elasticsearch’ün düzgün çalışıp çalışmadığını kontrol etmek için aşağıdaki komutu çalıştırabilirsiniz. Çıkan sonuçta elasticsearch versiyonunu, dizi adını (varsayılan olarak elasticsearch) ve diğer varsayılan değerleri görebilirsiniz.
![alt text](2.png)
