# Vaka yako wega SMTP mail kutumira server

## nhanganyaya

SMTP inogona kutenga zvakananga masevhisi kubva kune vatengesi vegore, senge:

* [Amazon SES SMTP](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali Cloud email push](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Iwe unogona zvakare kuvaka yako yega mail server - isina muganho kutumira, yakaderera yakazara mutengo.

Pazasi, isu tinoratidza nhanho nhanho magadzirirwo ekuvaka yedu yega mail server.

## Sevha sarudzo

Iyo yega-yakagadzirirwa SMTP server inoda IP yeruzhinji ine ports 25, 456, uye 587 yakavhurika.

Makore anowanzo shandiswa neveruzhinji akavharira madoko aya nekukasira, uye zvinokwanisika kuvhura nekupa basa rekuita, asi zvinonetsa mushure mezvose.

Ini ndinokurudzira kutenga kubva kune muenzi ane aya madoko akavhurika uye anotsigira kumisikidza reverse domain mazita.

Pano, ndinokurudzira [Contabo](https://contabo.com) .

Contabo mupi wekutambira anogara muMunich, Germany, yakavambwa muna 2003 nemitengo inokwikwidza.

Kana ukasarudza Euro semari yekutenga, mutengo uchave wakachipa (sevha ine 8GB ndangariro uye 4 CPUs inodhura anenge 529 yuan pagore, uye yekutanga yekuisa mari yemahara kwegore rimwe).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Kana uchiisa odha, taura `prefer AMD` , uye sevha ine AMD CPU ichaita zvirinani.

Mune zvinotevera, ini ndichatora Contabo's VPS semuenzaniso kuratidza maitiro ekuvaka yako wega mail server.

## Ubuntu system kumisikidza

Iyo inoshanda sisitimu pano ndeye Ubuntu 22.04

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

Kana sevha iri pa ssh ichiratidza `Welcome to TinyCore 13!` (sezvinoratidzwa mumufananidzo uri pasi apa), zvinoreva kuti iyo system haisati yaiswa. Ndokumbira ubvise ssh uye mirira kwemaminetsi mashoma kuti upinde zvakare.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

Kana `Welcome to Ubuntu 22.04.1 LTS` kunoonekwa, kutanga kwapera, uye unogona kuenderera nematanho anotevera.

### [Sarudzo] Tanga nharaunda yekusimudzira

Danho iri nderokusarudza.

Kuti zvive nyore, ndinoisa kuisirwa uye sisitimu yekumisikidza yeubuntu software mu [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) .

Mhanya unotevera kuraira kuti uise nekudzvanya kumwe chete.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Vashandisi veChinese, ndapota shandisai murairo unotevera pachinzvimbo, uye mutauro, nzvimbo yenguva, nezvimwe zvichaiswa otomatiki.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Contabo inogonesa IPV6

Vhura IPV6 kuitira kuti SMTP ikwanise kutumira maemail ane IPV6 kero.

gadzirisa `/etc/sysctl.conf`

Shandura kana kuwedzera mitsara inotevera

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

Tevedzera [necontabo tutorial: Kuwedzera IPv6 yekubatanidza kune server yako](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

Rongedza `/etc/netplan/01-netcfg.yaml` , wedzera mitsetse mishoma sezvakaratidzwa mumufananidzo uri pasi apa (Contabo VPS default configuration file yatova nemitsetse iyi, ingoisunungura).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Wobva `netplan apply` kuita kuti iyo yakagadziridzwa gadziriso iite.

Mushure mekugadzirisa kwabudirira, unogona kushandisa `curl 6.ipw.cn` kuona ipv6 kero yewekunze network.

## Clone iyo yekumisikidza repository ops

```
git clone https://github.com/wactax/ops.soft.git
```

## Gadzira yemahara SSL chitupa chezita rako rezita

Kutumira tsamba kunoda chitupa cheSSL chekunyora nekusaina.

Isu tinoshandisa [acme.sh](https://github.com/acmesh-official/acme.sh) kugadzira zvitupa.

acme.sh ndeye yakavhurika sosi otomatiki chitupa kusaina chishandiso,

Pinda gadziriro yekuchengetera ops.soft, mhanya `./ssl.sh` , uye `conf` folda ichagadzirwa **mudhairekitori repamusoro** .

Tsvaga mupi wako weDNS kubva [acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) , gadzirisa `conf/conf.sh` .

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Womhanya `./ssl.sh 123.com` kugadzira `123.com` uye `*.123.com` zvitupa zvezita rako redunhu.

Yekutanga kumhanya ichaisa otomatiki [acme.sh](https://github.com/acmesh-official/acme.sh) uye wowedzera basa rakarongwa rekuvandudza otomatiki. Iwe unogona kuona `crontab -l` , kune mutsara wakadaro sezvinotevera.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Iyo nzira yechitupa chakagadzirwa chinhu chakaita se `/mnt/www/.acme.sh/123.com_ecc。`

Kuvandudzwa kwechitupa kunodaidza `conf/reload/123.com.sh` script, gadzirisa iyi script, unogona kuwedzera mirairo senge `nginx -s reload` kuti uvandudze chitupa cache yezvikumbiro zvinoenderana.

## Vaka SMTP server ine chasquid

[chasquid](https://github.com/albertito/chasquid) inzvimbo yakavhurwa sosi SMTP server yakanyorwa nemutauro weGo.

Sechinotsiva echinyakare mail server zvirongwa sePostfix uye Sendmail, chasquid iri nyore uye iri nyore kushandisa, uye zvakare iri nyore kune yechipiri kusimudzira.

Mhanyai `./chasquid/init.sh 123.com` ichaiswa otomatiki nekudzvanya kumwe chete (tsiva 123.com nezita rako rekutumira).

## Gadzirisa Email Siginicha DKIM

DKIM inoshandiswa kutumira siginecha yeemail kudzivirira mavara kuti asabatwa sespam.

Mushure mekunge murairo waita zvinobudirira, iwe unozokurudzirwa kuseta iyo DKIM rekodhi (sezvinoratidzwa pazasi).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

Ingo wedzera TXT rekodhi kuDNS yako (sezvinoratidzwa pazasi).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Ona mamiriro ebasa & matanda

 `systemctl status chasquid` Ona mamiriro ebasa.

Mamiriro ekushanda kwakajairika sezvakaratidzwa mumufananidzo uri pasi apa

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` kana `journalctl -xeu chasquid` inogona kuona irogi rekukanganisa.

## Reverse domain name configuration

Reverse domain name ndeyekubvumidza iyo IP kero kuti igadziriswe kune inoenderana zita rezita.

Kuisa reverse domain name kunogona kudzivirira maemail kuti asaonekwe se spam.

Kana iyo tsamba yagamuchirwa, iyo inogamuchira sevha ichaita reverse reverse zita rekuongorora pane IP kero yekutumira server kuti ione kana iyo sevha yekutumira ine yakavimbika reverse domain zita.

Kana iyo sevha yekutumira isina reverse domain zita kana kana reverse domain zita risingaenderane ne IP kero yekutumira server, iyo inogamuchira sevha inogona kuona iyo email se spam kana kuiramba.

Shanyira [https://my.contabo.com/rdns](https://my.contabo.com/rdns) uye gadzirisa sezvakaratidzwa pasi apa

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Mushure mekuisa reverse domain zita, yeuka kugadzirisa kumberi kugadzirisa kwezita rezita ipv4 uye ipv6 kune server.

## Rongedza zita remukati rechasquid.conf

Shandura `conf/chasquid/chasquid.conf` kune kukosha kwereverse domain name.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Wobva wamhanya `systemctl restart chasquid` kuti utangezve sevhisi.

## Backup conf kune git repository

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Semuenzaniso, ini ndinodzosera iyo conf folda kune yangu github maitiro sezvinotevera

Gadzira imba yekuchengetera yakavanzika kutanga

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

Pinda iyo conf dhairekitori uye uendese kune warehouse

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Wedzera mutumi

run

```
chasquid-util user-add i@wac.tax
```

Inogona kuwedzera mutumi

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Iva nechokwadi chekuti password yakarongedzwa nemazvo

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

Mushure mekuwedzera mushandisi, `chasquid/domains/wac.tax/users` ichagadziridzwa, yeuka kuiendesa kunzvimbo yekuchengetera zvinhu.

## DNS wedzera SPF rekodhi

SPF (Sender Policy Framework) inyanzvi yekuongorora email inoshandiswa kudzivirira hutsotsi hwemaimeri.

Inoongorora kuti ndiani anotumira tsamba nekutarisa kuti IP kero yemunhu anotumira inoenderana here nemarekodhi eDNS ezita rezita raanoti ndiro, kudzivirira matsotsi kutumira maemail emanyepo.

Kuwedzera marekodhi eSPF kunogona kudzivirira maemail kubva pakuonekwa se spam zvakanyanya sezvinobvira.

Kana yako domain zita server isingatsigire SPF mhando, ingo wedzera TXT mhando rekodhi.

Semuenzaniso, SPF `wac.tax` yakaita seinotevera

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

SPF ye `_spf.wac.tax`

`v=spf1 a:smtp.wac.tax ~all`

Ziva kuti ndine `include:_spf.google.com` pano, izvi zvinodaro nekuti ndichagadzirisa `i@wac.tax` sekero yekutumira mubhokisi retsamba reGoogle gare gare.

## DNS kumisikidza DMARC

DMARC ndiyo chidimbu che (Domain-based Message Authentication, Reporting & Conformance).

Inoshandiswa kutora SPF bounces (zvichida zvichikonzerwa nezvikanganiso zvekugadzirisa, kana mumwe munhu ari kuzviita iwe kutumira spam).

Wedzera TXT rekodhi `_dmarc` ,

Zviripo ndezvizvi

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Zvinorehwa neparameter imwe neimwe ndezvizvi

### p (Gwaro)

Inotaridza mabatiro emaimeri anotadza kusimbiswa kweSPF (Sender Policy Framework) kana DKIM (DomainKeys Identified Mail). Iyo p parameter inogona kuiswa kune imwe yeatatu kukosha:

* hapana: Hapana danho rinotorwa, mhedzisiro chete yekusimbisa inodzoserwa kune anotumira kuburikidza neiyo email yekuzivisa maitiro.
* Quarantine: Isa iyo tsamba isati yapfuura ongororo mufolda yespam, asi haizoramba tsamba yacho zvakananga.
* ramba: Ramba zvakananga maemail anotadza kusimbiswa.

### fo (Sarudzo dzekutadza)

Inotsanangura huwandu hweruzivo rwakadzoserwa nenzira yekuzivisa. Inogona kuiswa kune imwe yeanotevera maitiro:

* 0: Taura mhinduro dzekusimbisa kune ese mameseji
* 1: Ingo shuma mameseji anotadza kusimbiswa
* d: Ingo shuma zita rezita rekutadza kutariswa
* s: taura chete SPF verification kukundikana
* l: Ingo shuma DKIM verification kukundikana

### rua & ruf

* rua (Kutaura URI yeAggregate mishumo): Email kero yekugamuchira yakaunganidzwa mishumo
* ruf (Kutaura URI yeForensic mishumo): email kero kuti ugamuchire yakadzama mishumo

## Wedzera marekodhi eMX kutumira maemail kuGoogle Mail

Nekuti handina kuwana bhokisi retsamba remahara rinotsigira kero dzepasirese (Catch-All, inogona kugamuchira chero maemail anotumirwa kune ino zita rezita, pasina zvirambidzo pane prefixes), ndakashandisa chasquid kutumira maemail ese kubhokisi retsamba reGmail.

**Kana iwe uine rako rakabhadharwa bhizinesi bhokisi retsamba, ndapota usagadzirise iyo MX uye svetuka nhanho iyi.**

Rongedza `conf/chasquid/domains/wac.tax/aliases` , seta bhokisi rekutumira

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` inoratidza maemail ese, `i` ndiyo email kero prefix yemushandisi anotumira akagadzirwa pamusoro. Kuti utumire tsamba, mushandisi wega wega anoda kuwedzera mutsara.

Wobva wawedzera iyo MX rekodhi (ini ndinonongedza zvakananga kukero yezita reverse rezita pano, sezvakaratidzwa mumutsara wekutanga mumufananidzo uri pazasi).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Kana kugadzirisa kwapera, unogona kushandisa mamwe maero eemail kutumira maemail ku `i@wac.tax` uye `any123@wac.tax` kuona kana uchikwanisa kugamuchira maemail muGmail.

Kana zvisiri, tarisa chasquid log ( `grep chasquid /var/log/syslog` ).

## Tumira email ku i@wac.tax neGoogle Mail

Mushure mekunge Google Mail yagamuchira tsamba, ini ndaitarisira kupindura `i@wac.tax` pane i.wac.tax@gmail.com.

Shanyira [https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) wodzvanya "Wedzera imwe email kero".

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Wobva waisa iyo yekusimbisa kodhi yakagamuchirwa neemail iyo yakatumirwa kwairi.

Pakupedzisira, inogona kuiswa sekero yekutumira (pamwe chete nesarudzo yekupindura nekero imwe chete).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Nenzira iyi, tapedza kugadzwa kweSMTP mail server uye panguva imwe chete kushandisa Google Mail kutumira uye kugamuchira maemail.

## Tumira email yekuedza kuti uone kana gadziriro yacho yabudirira

Pinda `ops/chasquid`

Mhanya `direnv allow` kuisa zvinotsamira (direnv yakaiswa mune yapfuura-kiyi yekutanga maitiro uye hoko yakawedzerwa kugoko)

wobva wamhanya

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Zvinorehwa nemaparamita ndezvizvi

* mushandisi: SMTP username
* pfuura: SMTP password
* ku: mugamuchiri

Unogona kutumira email yekuedza.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Zvinokurudzirwa kushandisa Gmail kugashira maemail ekuyedza kutarisa kuti zvigadziriso zvabudirira here.

### TLS yakajairwa encryption

Sezvinoratidzwa pamufananidzo uri pazasi, pane iyi kiyi diki, zvinoreva kuti chitupa cheSSL chakagoneswa zvinobudirira.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Wobva wadzvanya "Show Original Email"

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Sezvinoratidzwa pamufananidzo uri pazasi, iyo Gmail yepakutanga mail peji inoratidza DKIM, zvinoreva kuti iyo DKIM kumisikidzwa yakabudirira.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Tarisa iyo Yakagamuchirwa mumusoro weiyo email yekutanga, uye iwe unogona kuona kuti kero yekutumira ndeye IPV6, zvinoreva kuti IPV6 yakagadziridzwawo zvinobudirira.
