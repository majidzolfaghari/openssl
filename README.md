# openssl



براي صدور يك گواهينامه ssl  و كليد آن بايد در ابتدا يك درخواست امضا گواهينامه CSR توليد شود .  سپس CSR بايد به يك مركز صدور گواهينامه CA فرستاده شود تا گواهينامه SSL از سوي آن مركز صادر گردد . همچنين با استفاده از CA خود ميتوان يك گواهينامهSELF SIGED توليد كرد.
چگونگي ساخت يك CSR با استفاده از دستور هاي ابزار OPENSSL  به صورت زير است
#openssl req -new -newkey rsa:1024 -keyout hostkey.key-nodes -out hostcsr.csr

[root@sepahan ~]# openssl req -new -newkey rsa:1024 -keyout hostkey.key-out hostcsr.csr
Generating a 1024 bit RSA private key
...............................++++++
..++++++
writing new private key to 'hostkey.key'
Enter PEM pass phrase:
140662029551432:error:28069065:lib(40):UI_set_result:result too small:ui_lib.c:8                                                                             69:You must type in 4 to 1024 characters
140662029551432:error:0906406D:PEM routines:PEM_def_callback:problems getting pa                                                                             ssword:pem_lib.c:111:
140662029551432:error:0907E06F:PEM routines:DO_PK8PKEY:read key:pem_pk8.c:130:
[root@sepahan ~]# openssl req -new -newkey rsa:1024 -keyout hostkey.key-nodes -                                                                             out hostcsr.csr
Generating a 1024 bit RSA private key
.........................................++++++
..........................................................++++++
writing new private key to 'hostkey.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:ir                                      // نام كشور
State or Province Name (full name) []:ISF                                 //نام استان
Locality Name (eg, city) [Default City]:esf                               //نام شهر
Organization Name (eg, company) [Default Company Ltd]:IT                  // نام سازمان
Organizational Unit Name (eg, section) []:net                             //نام واحد سازماني
Common Name (eg, your name or your server's hostname) []:IT.net           //نام سرور
Email Address []:info@it.net                                              //ايميل

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:                                                  //درصورت تمايل يك پسورد براي آن ميگزاريم
An optional company name []:co                                            // نام شركت
FINISH

new : توليد يك CSR جديد   
New key rsa:1024 : توليد يك كليد خصوصي جديد از نوع rsa  با طول 1024 بايت 
Keyout hostkey.key: كليد خصوصي توليد شده را در فايل hostkey.keyجاي ميدهد ، اين فايل هنگامي كه گواهينامه ssl صادر ميشود مورد نياز ميباشد.
Out hostcsr.csr : csr توليد شده را رد فايل hostcsr.csr مينويسد سپس اين فايل را ميتواند به يك مركز توليد ssl ارايه داد و يا از آن براي توليد گواهينامه self signed استفاده نمود.

توليد گواهينامه self singed  با در اختيار داشتن csr
#openssl req –x509 -days 365 -in hostcsr.csr -key hostkey.key-out hostcert.cert
X509 : توليد گواهينامه self signe
Dayes 365 : تاريخ اعتبار گواهينامه self singed
In hostcsr.csr : csr را از فايل hostcsr.csr ميخواند
key hostkey.key: كليد خصوصي را از فايل  hostkey.keyميخواند .
out hostcert.cert : گواهينامه self signed را در فايل hostcert.cert ميريزد.


چنانچه بدانيم كه مستقيما به يك گواهينامه self singed نياز است و نيازي به امضاي آن توسط يك ca  نيست ميتوانيم گواهينامه self singed  را بدون توليد csr به صورت زير توليد كنيم
#openssl req –x509 -days 365 -newkey rsa:1024 -keyout hostkey.key-nodes -out hostcert.cert



مشاهده محتواي csr
پس از توليد فايل csr ميتوان محتواي آن را به كمك يك ويرايشگر متني مشاهده كرد كه محتواي آن به صورت كد شده ميباشد
براي مشاهده محتواي حقيقي فايل csr ميتوان از دستور زير استفاده كرد
#openssl req -text -noout -in hostcsr.csr
Text : مشاهده محتواي csr به صورت متن ساده
Noout : عدم نمايش نسخه pem-encoded در فايل csr
in hostcsr.csr : csr را از اين فايل ميخواند

مشاهده محتواي يك گواهينامه 
ميتوان گواهينامه را به كمك يك ويرايشگر متني مشاهده كرد . و در صورتي كه بخواهيم محتواي حقيقي آناز دستور زير استفاده مينماييم
#openssl x509 -text -noout -in hostcert.cert

مشاهده امضا كننده يك گواهينامه
به ca كه يك گواهينامه را امضا كرده است صادر كننده گفته ميشود . كه ميتوان صادر كننده گواهينامه و يا hash ان را مشاهده كرد
به كمك دستور زير
#openssl x509 -in hostcert.cert -noout -issuer -issuer_hash
in hostcert.cert : گواهينامه را از فايل hostcert.cert ميخواند
noout : عدم نمايش نسخه pem-encoded فايل گواهينامه
Issuer : نام جداگانه DN صادر كننده را چاپ ميكند
Issuer _hash : hash ، نام صادر كننده را چاپ ميكند

