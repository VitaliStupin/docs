# Sissejuhatus

**X-tee keskkonnad ja nendega liitumine**: https://moodle.ria.ee/mod/book/view.php?id=323&chapterid=53

**Sammud X-tee kasutuselevõtul**: https://moodle.ria.ee/mod/book/view.php?id=338&chapterid=173 

**X-tee kasutamise juhend**: https://moodle.ria.ee/mod/page/view.php?id=288

**Eeldused**:

- paigaldatud on v6 turvaserver
- paigaldatud ja häälestatud on andmejälgija komponendid vastavalt tarkvara paigaldamise juhendile: https://github.com/e-gov/AJ/blob/master/doc/Paigaldamine.md).

Andmejälgijat paigaldades tuleb seadistused valida selliselt, et andmejälgija ei jälgiks päringuid, mis on tehtud niisuguste politseiliste menetlustoimingute osana, mis peavad jääma varjatuks: https://github.com/e-gov/AJ/blob/master/doc/Rakendusjuhend.md#milliseid-andme-edastamisi-ja--kasutamisi-logida-ja-milliseid-mitte.

Päringute logimise keelamiseks saab tüüplahenduse juures kasutada jälgimisfiltri konfiguratsiooni osaks olevat nn blacklist'i: täpsemalt paigaldamisjuhendi peatükis "Välistuste kirjeldamine" https://github.com/e-gov/AJ/blob/master/doc/Paigaldamine.md#v%C3%A4listuste-kirjeldamine.

Küsimuste korral võtke ühendust RIA kasutajatoega aadressil help@ria.ee (teema: andmejälgija paigaldamine).

X-tee v6 erinevates keskkondades olemas olevate asutuste teenuste kohta saab infot siit: http://x-road.eu/allmethods/

Soovitavalt tuleks sama alamsüsteemi nimetust kasutada kõikides X-tee keskkondades.

# Andmejälgija teenuse testimine riigiportaali partnerite arenduskeskkonnas www.koolitus.eesti.ee

- Alamsüsteemi loomine ja registreerimine (vajadusel)
  * Andmejälgija teenust võib osutada mõne olemasoleva alamsüsteemi kaudu.
  * Kui teenusepakkuja otsustab andmejälgija teenuse pakkumiseks luua eraldi alamsüsteemi, tuleb see oma X-tee arendus- või testkeskkonna turvaserveri kaudu registreerimisele esitada.
  * X-tee keskus rahuldab alamsüsteemi taotluse üldjuhul paari tööpäeva jooksul. Turvaserveri kasutajaliideses annab sellest märku alamsüsteemi juures olev märge 'Registered'.
- Andmejälgija teenuse avamine RIA-le
  * Teenusepakkuja peab oma X-tee v6 arendus- või testkeskkonna turvaserveris avama RIA-le andmejälgija teenuse 'findUsage':
    + arenduskeskkonnas alamsüsteemile: `ee-dev : GOV : 70006317 : riigiportaal-citizen`,
    + testkeskkonnas alamsüsteemile: `ee-test : GOV : 70006317 : riigiportaal-citizen`.
- Teenuse avaldamine ja testimine www.koolitus.eesti.ee keskkonnas.
  * Teenusepakkuja edastab RIA kasutajatoele (help@ria.ee) soovi hakata partnerite arenduskeskkonnas teenust testima. Sealhulgas tuleb nimetada:
    + X-tee keskkond (arendus- või testkeskkond),
    + alamsüsteemi nimi, mille vahendusel teenust pakutakse,
    + www.koolitus.eesti.ee keskkonnale ligipääsude loomiseks testijate andmed (kui neid pole varem taotletud):
      - nimi
      - isikukood
      - asutus
  * Riigiportaali teenusehaldur lisab seejärel www.koolitus.eesti.ee keskkonnas teenuse vormi, testib selle toimimist ning annab teenusepakkujale teada, kui see võib asuda ka ise testima.

# Andmejälgija teenuse avaldamine riigiportaali toodangukeskkonnas

- Alamsüsteemi loomine ja registreerimine (vajadusel); teenuse info lisamine RIHAs
  * Andmejälgija teenust võib osutada mõne olemasoleva alamsüsteemi kaudu.
  * Kui teenusepakkuja otsustab andmejälgija teenuse pakkumiseks luua eraldi alamsüsteemi, tuleb see RIHA keskkonnas ja oma X-tee toodangukeskkonna turvaserveri kaudu registreerimisele esitada (https://moodle.ria.ee/mod/page/view.php?id=288 p.4).
    + X-tee keskus rahuldab alamsüsteemi taotluse üldjuhul paari tööpäeva jooksul, sellest antakse teada RIHAs alamsüsteemi juures märgitud kontaktisikule.
  * Teenusepakkuja lisab RIHAs alamsüsteemile teenuse info:
    + infosüsteemi ülem laadib RIHAs alamsüsteemi teenuste vaates üles teenuse WSDLi (nupp 'Laadi infosüsteemi X-tee v6 keskkonna teenuste WSDL')
- Andmejälgija teenuse avamine RIA-le.
  * Teenusepakkuja avab oma X-tee v6 toodangukeskkonna turvaserveris andmejälgija teenuse `findUsage` RIA alamsüsteemile: `EE : GOV : 70006317 : riigiportaal-citizen`.
- Taotluse esitamine teenuse riigiportaalis avaldamiseks ja teenuse avaldamine
  * Teenusepakkuja täidab andmejälgija teenuse registreerimise taotluse (https://www.ria.ee/public/Riigiportaal/aj_teenuse_taotlus.pdf).
  * Taotluse peab allkirjastama asutuse allkirjaõiguslik isik.
  * Taotlus tuleb edastada RIA kasutajatoe aadressile help@ria.ee. 
  * Kui teenus on riigiportaalis avaldatud, antakse sellest taotluse esitajale teada.

# Kasutajatugi, hooldustööd ja katkestused

https://www.eesti.ee/est/riigiportaali_abi/info_e_teenuse_tegijale/teenuse_avamine_muutmine_ja_sulgemine/kasutajatugi_hooldustood_ja_katkestused

- Teenusele osutab esmast kasutajatuge RIA. Kui kasutajatugi ei saa kasutajat kohe aidata, suunab ta küsimuse edasi teenuseomanikule. 
- Kõikide RIA kasutajatoelt teenuse omanikule suunatud lõppkasutajate pöördumiste lahendused tuleb edastada ka RIA kasutajatoele. See tähendab, et kasutajale e-posti teel vastates tuleb edastada koopia RIA kasutajatoele (help@ria.ee) koos viitega pöördumise ID-numbrile.
- RIA kasutajatoelt saadetud e-kirjadele tuleb edastada automaatvastus, mis sisaldab hiliseimat vastamistähtaega.

# Katkestused teenuse töös

- Teenusega seotud hooldustöödest ja planeeritud katkestustest peab teenusepakkuja RIA kasutajatuge teavitama (lisaks RIHA kaudu edastatavale teatele teenuse kasutajatele) vähemalt 48 tundi enne katkestuse toimumist (nädalavahetusel ja/või esmaspäeval toimuvatest katkestustest tuleb teavitada hiljemalt reedel kell 10.00).
- Katkestuse info peab olema selge, lõppkasutajale arusaadav ja sobilik riigiportaalis avaldamiseks.
- Teenusega seotud planeerimata katkestustest peab teenusepakkuja RIA kasutajatuge aadressil help@ria.ee teavitama niipea kui võimalik, märkides võimalusel ära katkestuse eeldatava lõppaja.
- RIA kasutajatugi teavitab teenusepakkujat riigiportaali üldistest hooldustöödest ja katkestustest teenusepakkuja edastatud e-posti listiaadressil vähemalt 48 tundi enne nende toimumist.

# Teenuse tööaeg ja katkestuste kestus

- Teenuse tööaeg ja kasutajatoe aeg peab olema vähemalt E–R kl 9–17.
- Teenuse tööajal planeeritud katkestuste maksimaalne kogukestus aastas võib olla kuni 24 tundi.
- Teenuse ühe planeeritud katkestuse maksimaalne kestus võib olla kuni kaks tundi.
- Soovituslikult võiks planeeritud katkestusi teenuse tööajal olla ühes kuus kaks.
- Teenuse tööajal planeerimata katkestuste kogukestus aastas võib olla kuni 24 tundi.
- Teenuse ühekordse planeerimata katkestuse kestus võib olla kuni 12 tundi.
