<!--author=alkohli last changed:02/22/16-->

#### <a name="to-attach-the-sas-cables"></a>Att koppla SAS-kablar
1. Identifiera primära och EBOD bilagor. Två höljena kan identifieras genom att titta på sina respektive tillbaka plan. Finns i följande bild för vägledning. 
   
    ![Säkerhetskopiera plan av primära och EBOD bilagor](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Bakifrån primära och EBOD bilagor**
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Primär enhet |
   | 2 |EBOD hölje |
2. Leta upp serienummer på den primära servern och EBOD bilagor. Serienummer etiketten fästs på varje höljet tillbaka AX. Serienumret måste vara identiska på båda höljen. [Kontakta Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) omedelbart om serienumret inte matchar. Se bilden nedan för att hitta serienummer.
   
    ![Bakifrån höljet visar serienummer](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Platsen för serienummer etikett**
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |Ta bort av inneslutningen |
3. Använd angivna SAS-kablar för att ansluta EBOD höljet till primära höljet på följande sätt:
   
   1. Identifiera de fyra SAS-portarna på primära höljet och EBOD höljet. SAS-portar är märkta som EBOD på primära höljet och motsvarar en port på höljet EBOD enligt SAS-kablar illustrationen nedan.
   2. Använd de angivna SAS-kablarna för att ansluta EBOD port till port A.
   3. EBOD-port på styrenhet 0 ska anslutas till port A på EBOD styrenhet 0. EBOD-port på en domänkontrollant 1 ska anslutas till port A på EBOD kontrollant 1. Se följande illustration anvisningar. 
      
      ![SAS-kablar för din enhet](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS-kablar**
      
      | Etikett | Beskrivning |
      |:--- |:--- |
      | A |Primär enhet |
      | B |EBOD hölje |
      | 1 |Styrenhet 0 |
      | 2 |Kontrollant 1 |
      | 3 |EBOD styrenhet 0 |
      | 4 |EBOD kontrollant 1 |
      | 5, 6 |SAS-portar på primära hölje (märkt EBOD) |
      | 7, 8 |SAS-portar på EBOD hölje (Port A) |

