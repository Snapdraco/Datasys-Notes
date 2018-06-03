# Datasystemdesign Notater

# Kapittel 6
## Assembly language

**Addition**, a=b + c -> ADD a, b, c
**Subtraction**, a=b - c -> SUB a, b ,c

## Design principles
**1. Regularity supports simplicity**
Instruksjoner med et fast antall operander er lettere å kode og lettere for hardware å håndtere. 

**2. Make the common case fast**
ARM instruksjonene gjør "the common case" rask ved å bare inkludere enkle og godt kjente instruksjoner. Mer avanserte funksjoner er som et puslespill satt sammen av disse simple instruksjonene. 

**3. Smaller is faster**
Det er raskere å finne og hente data fra små registere. Større registere har mer plass men de er tregere. 

**4. Good design demands good compromises**
ARM sitt komprimis er å definere 3 hovedinstruksjons format. 
- Data-prosessering, Har ett første og andre source min

# Skriv mer om 4.

Arm er en RISC(reduces instruction set computer) arkitektur altså den baserer seg på enkle og raske instruksjoner. Prosessorer som Intel x86 er en CISC (complex instruction set computer) arkitektur. 


### Register
Operander lagret i minne vil ta lang tid å hente. Derfor bruker vi registere i de aller fleste arkitekturer. Registere er mindre og raskere enn minnet. ARM har 16 registere som kalles register set eller register fil. Jo ferre registere man har desto raskere finner man data i de.

**Assembly with registers**
ADD R0, R1, #12 - Legger sammen 12 og verdien i R1 og legger svaret i register 2

SUB R2, R3, R4 - Trekker verdien i R4 fra verdien i R3 og legger resultatet i R2

**Register set**
**R0** - Argumeneter/Return Values/Temp Variabler
**R1-R3** - Argumenter/Temp variabler
**R4-R11** - Lagrede variabler
**R12** - Temp variabler
**R13** - Stack pointer
**R14** - Link register
**R15** - Program counter

### Minne
Minnet er mye større og tregere enn registerene. ARM benytter seg av byte adressable minne, hver byte i minnet har altså en unik adresse. ARM opererer eksklusivt ifra registere, data lagret i minne må altså flyttes til er register før bruk. 
- LDR brukes for å bevege data fra minne til et register
- STR brukes for å bevege data fra et register til minne

## ARM Programming
Arm har støtte for logiske operasjoner som AND, OR, XOR osv

### Shift instruction 
Denne funksjonen "shifter" verdien i et register mot høyre eller venstre. (LSR & LSL) vi har også noe som kalles Aritmetisk Shift Right(ASR) og Rotate right (RoR)

L Shift -> Dytter inn nuller 
AR Shift -> Dytter in 1 eller 0 basert på verdien til MSB
ROR -> Dytter ut stupet så går de inn igjen bakerst i køa

### Condition flags
Setter flagg basert på resultatet/outputen. Instruksjoner kan utføres ut ifra hvilke flagg som blir satt. ARM flag = negative (N), Zero (Z), Carry (C), og Overflow (O). Disse flaggene settes av ALU'en og som oftest ved hjelp av CMP funksjonen. Vi trekker den andre source operanden fra den første og setter flagg basert på resultatet. Flaggene er de første 4 bitsene i CPSR (Current Program Status Register).

### Branching 
ARM bruker branches for å gjøre "avgjørelser". Når man bruker en Branch funksjon endrer man Program Counter(PC). ARM har to typer Branch funksjoner Branch (B) og Branch and Link (BL). BL benyttes i funksjonskall. 

### Minne
Lik data kan grupperes i en array for "ease of storage". En array lagrer data i sekvensielle data adresser (etter hverandre). Hvert element i denne arrayen defineres av en index

### ASCII 
Tildeler hver bokstav en unik byte verdi. Disse verdiene er riktignok representert på hexadecimal format istedet for bytes. 

### Function Calls
Lignende metoder i high-level programmering. Man bruker kode om igjen ved å kalle på en tidligere funksjon. Den som kaller og den som blir kalt fra må bli enige om hvor verdiene skal lagres. **Skriv potensiellt mer**

### Stack
Stacken er minne som benytter til å lagre informasjon inne i en funksjon. Stacken utvider og krymper ut ifra hvor mye minne prosessoren krever. En stack er en Last In First Out (LIFO) kø. Den siste som legges inn er den første som hentes ut. Stack pointeren peker til toppen av stacken. En av de viktige oppgavene til stacken er å lagre og gjenopprette registere som brukes av en funksjon. Stacken lagrer innholdet til registerene før den modifiserer de, deretter gjenoppretter den registeret etter at funksjonen har gjort operasjonene sine. 


# Kap 7

## Design Process

### Program Counter (PC)
Er logisk en del av register fila. Blir lest og skrevet til for hver syklus uavhengig av det vanlige registeret. Er mer naturlig bygd som et separat 32-bits register. Outputen peker til den nåværende instruksjonen mens inputen indikerer adressen til neste instruksjon. 

### Instruksjons Minne
Har én read port tar en 32-bits instruksjon input og leser fra den addressen ut til outputen. Input A og Output RD. 

### Register Filen
Vi har femten 32-bits register R0-R14. og en ekstra input for å motta PC i R15. 
- A1 og A2 definerer hvilke source operander den skal bruke 
- A3 definerer destinasjons registeret som skal skrives til.  
- En 32-bits Write-data input. 
- En Write-enable input
- og til slutt en klokke.


Hvis Write-Enable signalet er på vil register fila skrive til destinasjons registeret ved rising edge på klokken. En read fra R15 vil returnere PC+8. Writes til R15 må spesialhåndteres for å oppdatere PC verdien fordi den er separat fra registeret. 

### Data minnet
Har en read/write port. Hvis Write Enable er 1 skriver den data fra WD til adressen A ved rising edge. Hvis Write Enable er 0 leser den adressen A ut til RD. 


Instruksjons minnet, register fila og data minner leses alle kombisjonellt. Så hvis adressen endres vil ny data komme til RD etter litt Propagation delay. 


## Microarchitectures 

### Single Cycle mikroarkitektur
- Utfører en hel instruksjon på én syklus.
- Syklustiden avgjøres av tregeste instruksjon (LDR STR)
- Krever at prosessoren har et separat instruksjons og data minne. 


### Multicycle mikroarkitektur
- Utfører instruksjoner i flere kortere sykluser
- Bruker hardware om igjen noe som sparer kostnader
- En instruksjon om gangen, men hver instruksjon bruker flere klokke sykluser. 
- Krever bare ett minne. 

### Pipelined mikroarkitektur
- Flere instruksjoner samtidig
- Må kunne takle "dependencies"/avhengigheter
- Må ha tilgang til instruksjoner og data i samme syklus
- Bruker ofte separate instruksjons og data caches
- Alle nye high-end prosessorer er pipeline prosessorer. 



## Pipeline Prosessor 
En pipeline prosessor deler single cycle prosessoren inn i 5 deler. Hvert steg gjør en separat oppgave. Disse stegene er Fetch, Decode, Execute, Memory og Writeback. 

**Fetch**
Prosessoren leser instruksjonen fra instruksjons minnet

**Decode** 
Prosessoren leser source operanden fra register fila og dekoder instruksjonen for å lage kontroll signaler.

**Execute**
Prosessoren utfører beregninger med ALU'en

**Memory**
Prosessoren leser eller skriver data-minnet.

**Writeback**
Prosessoren skriver tilbake til register fila hvis den mottar rett kontrollsignal. 

## Hazards

Kan oppstå hazards når en instruksjon er avhengig av resultatet til en annen instruksjon som ikke er ferdig enda. 

### Read after write (RAW)
Oppstår hvis en instruksjon bruker en verdi som en tidligere instruksjon skal skrive til, men henter den før den er skrevet til. 

Kan løses ved å legge en NOP mellom funksjonene, men dette er ikke ideellt ettersom at det kompliserer programmeringen og går ut over performance. 

#### Forwarding 
Vi kan forwarde et resultat fra memory eller writeback fasen av en instruksjon til en senere instruksjon som får bruk for verdien. For å gjøre dette trenger vi multiplexere foran ALU'en så man kan velge operander fra enten memory eller writeback stagen. 

Forwarding er nødvendig når en senere instruksjon har likt source register som matcher destinasjons registeret til en tidligere instruksjon. 

Vi trenger også en Hazard unit som mottar signaler fra control uniten og leser verdiene i multiplexerne våre for å avgjøre hvor infoen kommer fra og hva den skal gjøre med den. 

#### Stalls 
Forwarding kan fikse RAW data hazards når resultatet regnes ut i execute stagen, men i LDR blir ikke beregningene ferdig før på slutten av memory stagen. dermed kan den ikke forwardes til execute stagen til neste instruksjon. LDR har noe vi kaller two-cycle latency fordi en avhengig instruksjon kan ikke bruke resultatet dens før etter to sykluser. 

Stalls 
Delayer instruksjoner og signaler slik at verdiene den er avhengig av rekker å oppdatere seg. En stall vil stall alle følgende instruksjoner også slik at det ikke oppstår rot med dataen. Etter en stall å pipeline registeret flushes slik at det ikke ligger feil info der. Stalls går ut over performance å burde bare brukes når nødvendig. 

Implementeres ved hjelp av Enable signaler koblet til fetch og decode fase registerene og et synkront reset/clear signal til execute registeret




lager noe som kalles en boble som stopper all informasjon og signaler fra å 


#### Branch prediciton
Branches skaper en kontroll hazard ettersom at vi vet ikke hvilken instruksjon vi skal hente før etter lang tid. Dette kan løses med stalls som går enormt ut over performance. Vi bruker helst branch prediction som baserer seg på at vi gjetter hvor branchen skal hoppe og begynner å utføre instruksjoner. Hvis dette er feil flusher vi pipelinen og begynner med de virkelige instruksjonene. Mengden instruksjoner flushet kalles en penalty. Vi kan redusere penaltyen ved å predicte tidligere. 

### Advanced Microarchitecture 

#### Deep pipelining 
Prinsippet bak deep pipelining er å kutte prosessoren opp i enda flere deler slik at hver del inneholder mindre logikk så hvert steg kjører raskere. Max-antallet av pipeline seksjoner begrenses av hazards. Jo flere pipeline seksjoner man har jo flere dependencies kan oppstå. På grunn av setup-timen til pipeline registerene vil for mange pipeline stages begynne å gi diminishing returns samt øke kostnadene ytterligere. 

#### Micro operation
Gjør the common case raskere ved å splitte mer avanserte funksjoner inn i mindre og simplere mikro funksjoner. En enkelt kompleks funksjon vil riktignok bruke mindre minne enn den ekvivalente kombinasjonen av simple instruksjoner. Så man må gjøre et valg basert på cost og performance. 

#### Branch prediction 
Dypere pipelines vil ha en større penalty for å predicte branches feil ettersom at branchen resolves ikke før senere i pipelinen. 

Vi bruker branch predictors for å gjette om branchen skal taes eller ikke. 

**Statisk Branch Prediction**
Tar ikke hensyn til program historien. 

**Dynamisk Branch Prediciton**
Bruker programhistorien og gjetter om branchen skal tas ut ifra branch target buffer tabellen. 

**One-bit dynamic branch predictor**
Ser om branchen ble tatt eller ikke sist gang og gjetter at den vil gjøre det samme denne gangen. hvis det er en loop vil den gjette feil den siste gangen og feil den første gangen hvis den kjøres igjen. 

**Two-bit dynamic branch predictor**

Har 4 states: Strongly taken, weakly taken, weakly not taken, strongly not taken. Når en loop gjentar seg vil den være i strongly not taken å gjette at den ikke blir tatt helt til den siste iterasjonen hvor den vil bli tatt. Den går da til weakly taken å gjetter not taken hvis loopen kjører igjen. Her vil det bare oppstå feil på slutten. 

#### Superscalar processor 
Inneholder flere kopier av hardwaren slik at den kan utføre flere instruksjoner samtidig. For eksempel kan den hente inn to instruksjoner om gangen å utføre disse samtidig. Dette kan være vanskelig pga dependencies. Superskalar benytter både temporal og spacial paralellisering. 

#### Out-of-order Processor
En out of order processor sjekker instruksjonene og ser etter de uten noen dependencies og utfører disse så rask som mulig. Den bruker en tabell kalt et scoreboard for å holde styr på dependencies. Bruken av Out-of-order prosesser introduserer to nye hazards. WAR og WAW. 

#### WAR
Write after read. Denne hazarden dukker opp når vi skriver til ett register før den leses av en annen instruksjon noe som leder til at den som leser mottar feil verdi. Dette vil vi unngå. Hazarden oppstår som et resultat av programmererens valg. 

#### WAW
Write after write. Når vi skriver til et register som en annen funksjon akkurat har skrevet til slik at vi overskriver en nylig endring uten at den blir brukt til noe. Dette vil vi unngå. Hazarden oppstår som et resultat av programmererens valg. 


#### Register Renaming
Er en teknikk som brukes av out-of-order prosessoren for å unngå WAR og WAW hazards. Register renaming legger til noen non-architectural registere som prosessoren kan benytte seg av. Programmereren derimot kan ikke se eller programmere mot disse registerene. Bruken av register renaming vil kunne øke performance. 

#### Multithreading

En prosess består av flere threads. En thread håndterer en mindre del av en prosess. I vanlige prosessorer gir bare threads brukeren illusjonen om at de kjører paralellt ved å veksle mellom seg i høy hastighet. Denne vekslingen styres av operativ systemet. 

Når en thread er ferdig vil maskinen/OS'en lagre dens architectural state og laste inn neste thread sin architectural state. 

En multithreaded processor har flere kopier av en thread sin architectural state slik at mer enn én thread kan være aktiv om gangen. Multithreading øker ikke en enkelt thread sin performance men vil kunne øke prosessorens throughput. 

#### Multiprocessors

Et multiprocessor system består av flere prosessorer og en måte å kommunisere mellom de på. 

Det er tre klasser av multiprosessorer: Symmetriske(homogene), Heterogene og clusters. 

#### Symmetriske multiprosessorer
Inkluderer to eller flere identiske prosessorer med ett delt hovedminne. De ulike prosessorene kan være separate chipper eller ulike kjerner på samme chip. 

Multiprosesorene kan brukes for å kjøre flere threads samtidig eller gjøre en enkelt thread raskere. Å kjøre flere threads samtidig er en enkel sak ettersom at du bare fordeler de på de ulike prosessorene. Å få en thread til å gå raskere derimot er mer komplisert. Man må da måtte dele denne ene threaden inn i mindre threads som fordeles ut til prosessene. Altså threads inne i threads, threadception. Utfordringen ligger i å få disse threadene til å kommunisere med hverandre. 

Dette er relativt enkelt å designe fordi man kan designe en prosessor og copy-paste de for å øke performance. Det er også relativt lett å programmere opp mot. 

#### Heterogene multiprosessorer
For å forbedre power-performance forholdet implementerer heterogene multiprosessorer spesialisert hardware som egner seg til spesifikke oppgaver. En applikasjon vil så benytte seg av den typen hardware som egner seg best til sitt formål. Et heterogent system kan benytte kjerner med ulik mikroarkitekturer og trade-offs. 

**big.LITTLE**
Er en design strategi popularisert av ARM. Systemet inneholder både energy-efficient og high-performance kjerner som hver gjør ulike oppgaver. LITTLE kjernene tar seg av vanlige og mindre krevende oppgaver mens big kjernene tar hån om større og mer avanserte oppgaver. 

**Acellerators**
En annen strategi er acellerators. Dette er hardware optimalisert for en spesifikk oppgave og gjør akkurat denne oppgave mye bedre enn alle de andre komponentene. En GPU er et eksempel på en accelerator som prosesserer grafikk mye raskerer enn for eksmepel prosessoren. 

Heterogene multiprosessorer er mer komplekse å designe å programmere mot. 

#### Clusters
Hver prosessor har et eget lokalt minne system. 

En type cluster er flere datamaskiner koblet sammen via et nettverk som kjører en software for å løse et større problem sammen. 

Datasentere er også et eksempel på clusters. Der har vi datamaskiner og disker som er koblet sammen i stativer og deler strømforsyning og kjøling. 