<!--author=alkohli last changed: 01/12/17-->

<a id="to-complete-the-minimum-storsimple-device-setup" class="xliff"></a>

#### Slutför den minimala StorSimple-enhetsinställningen

1. Markera och klicka på din enhet i tabellistan med enheter på bladet **Enheter**. Enheten har statusen **Redo för installation**. Bladet **Konfigurera enhet** öppnas.

     ![Nätverksgränssnitt för minimala StorSimple-enhetsinställningar](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. Bladet **Konfigurera enhet**:
   
   1. Ange ett **eget namn** för din enhet. Standard-enhetsnamnet har information som enhetsmodell serienummer. Du kan tilldela ett eget namn på upp till 64 tecken för att hantera din enhet.
   2. Ange **tidszon** baserat på den geografiska plats där enheten ska distribueras. Enheten använder den här tidszonen för alla schemalagda åtgärder.
   3. Under **Inställningar för DATA 0**:

       1. DATA 0-nätverksgränssnittet visas som aktiverat med nätverksinställningarna (IP, undernät och gateway) som konfigurerats via guiden. DATA 0 aktiveras också automatiskt för molnet samt iSCSI.

       2. Ange de fasta IP-adresserna för Controller 0 och Controller 1. **De styrenhets-fästa IP-adresserna måste vara fria IP-adresser i undernätet som är åtkomliga från enhetens IP-adress.** Om DATA 0-gränssnittet har konfigurerats för IPv4, måste de fasta IP-adresserna anges i IPv4-format. Om du angav ett prefix för IPv6-konfigurationen fylls de fasta IP-adresserna i automatiskt i fälten.

            ![Nätverksgränssnitt för minimala StorSimple-enhetsinställningar](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            De fasta IP-adresserna för styrenheten används för att hantera uppdateringarna till enheten. Därför måste de fasta IP-adresserna vara dirigerbara och kunna ansluta till Internet. Du kan kontrollera att de fasta IP-adresserna för dina styrenheter är dirigerbara med hjälp av cmdleten [Test-HcsmConnection][Test]. Följande exempel visar att fasta styrenhets-IP-adresser dirigeras till Internet och har åtkomst till Microsoft Update-servrarna.

            ![Test-HcsmConnection visar dirigerbara IP-adresser](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Klicka på **OK**. Enhetskonfigurationen startar. Du meddelas när enhetskonfigurationen är klar. Enhetens status ändras till **Online** på bladet **Enheter**.

    ![Nätverksgränssnitt för minimala StorSimple-enhetsinställningar](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

   > [!NOTE]
   > Du kan ändra alla andra enhetsinställningar när du vill på bladet **Enhetsinställningar**.

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx