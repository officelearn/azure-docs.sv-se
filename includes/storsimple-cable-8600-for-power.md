<!--author=alkohli last changed: 9/16/15-->


#### <a name="to-cable-your-device-for-power"></a>Att kabelanslut din enhet för ström
> [!NOTE]
> Båda höljen på StorSimple-enheten inkluderar redundant PCMs. PCMs måste installeras och ansluten till olika strömkällor att säkerställa hög tillgänglighet för varje enhet.
> 
> 

1. Kontrollera att strömbrytare på alla PCMs i OFF-läge.
2. Anslut strömkablar till båda PCMs från primära höljet. Strömkablar identifieras i rött i power går diagrammet nedan.
3. Kontrollera att två PCMs på primära enheten använder separata strömkällor.
4. Bifoga strömkablar slå på rack distribution enheter som visas i power kablar diagram.
5. Upprepa steg 2 till 4 för EBOD höljet.
6. Aktivera EBOD höljet genom att vända på strömknappen på varje PCM till på plats.
7. Kontrollera att EBOD höljet är aktiverat genom att kontrollera att grön indikatorer på baksidan av EBOD domänkontrollant är aktiverade.
8. Aktivera primära höljet genom att vända varje PCM växeln till på plats.
9. Kontrollera att systemet är på genom att säkerställa enhetskontroll indikatorer har aktiverat.
10. Kontrollera att anslutningen mellan EBOD domänkontrollanten och enhetskontroll är aktiva genom att verifiera att fyra led bredvid SAS-port på EBOD domänkontrollant är grön.
    
    > [!IMPORTANT]
    > För att säkerställa hög tillgänglighet för ditt system, rekommenderar vi att du noggrant följer power kablar schemat visas i följande diagram.
    > 
    > 
    
    ![Kabelansluta den 4U för ström](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Kablar**
    
    | Etikett | Beskrivning |
    |:--- |:--- |
    | 1 |Primär enhet |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Styrenhet 0 |
    | 5 |Kontrollant 1 |
    | 6 |EBOD styrenhet 0 |
    | 7 |EBOD kontrollant 1 |
    | 8 |EBOD hölje |
    | 9 |PDU |

