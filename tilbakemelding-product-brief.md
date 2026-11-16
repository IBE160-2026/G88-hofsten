# Tilbakemelding på product brief

| | |
|---|---|
| **Gruppe** | G88 – G88-hofsten |
| **Product brief** | `product-brief.md` (commit `d488ff7`) |
| **Tilbakemelding fra** | Faglærer i IBE160 (utarbeidet med KI-støtte) |
| **Dato** | 2026-10-06 |

## Samlet vurdering

- **Godt utgangspunkt med justeringer.** Gruppen kan gå videre og innarbeide punktene under.

**Det som er bra:**

1. Kjerneflyten i VoyageAI er svært tydelig og passe avgrenset: sett opp en reise med 3–5 anløp, registrer én forsinkelse (for eksempel «36 timer sen avgang fra havn 2»), se konsekvensene, sammenlign tre scenarioer (behold fart, øk fart, senk fart til neste kaivindu), les KI-forklaringen og ta beslutningen selv.
2. Rollefordelingen er gjennomtenkt: koden beregner ETA, ventetid og kostnad deterministisk, KI forklarer avveiningene og får ikke innføre egne tall, og planleggeren beslutter. Suksesskriteriet om at en håndkontroll skal bekrefte tallene og at KI-forklaringen ikke motsier dem, er konkret og testbart. Briefen er også ærlig om at problemet ikke er validert med brukere.

**De viktigste endringene:**

1. Lukk de åpne spørsmålene om kostnadsmodellen før PRD. Bestem hvilke kostnadselementer som er med (for eksempel drivstoff, ventetid ved kai og havneavgift), og hvilken sammenheng mellom fart og forbruk dere bruker (for eksempel at forbruket øker med farten i tredje potens). Skriv formlene ned, slik at de kan bli fasit for tester.
2. Planlegg hvordan sensor kan kjøre appen uten deres KI-nøkkel. Scenarioberegningene bør virke uten KI, og KI-forklaringen bør ha en mock-modus eller et forhåndsgenerert eksempel for prøvereisene.
3. Briefen viser til `addendum.md` for kilder om fart, forbruk og havnedata, men fila ligger ikke i repoet. Legg den inn, slik at grunnlaget for de simulerte tallene er sporbart.

## Vanskelighetsgrad og gjennomførbarhet

### Vurdert vanskelighetsgrad

- **Middels**

**Sammenlignbart med:** 7) Kurs-FAQ-chatbot (middels) når det gjelder krav til kvaliteten på KI-svaret, men med mer regelbasert domenelogikk i form av ETA-, ventetids- og kostnadsberegninger.

**Begrunnelse:**

| Faktor | Nivå (lav / middels / høy) | Kommentar |
|---|---|---|
| Domenelogikk – hvor mange og hvor kompliserte regler og beregninger må stemme? | Middels | ETA-forplantning gjennom anløpene, kaivinduer, ventetid, fart–forbruk og kostnad for tre scenarioer. Overkommelig, men alt må stemme sammen. |
| Datamodell – antall entiteter og relasjoner mellom dem | Lav | Fartøy, reise, anløp med planlagte tider og kaivinduer, forsinkelse og scenarioresultat. |
| Brukere, roller og innlogging | Lav | Ingen brukerkontoer i v1. |
| KI-funksjonalitet i appen, f.eks. kall til språkmodell, prompts i koden og håndtering av usikre svar | Middels | Én KI-forklaring per sammenligning, med krav om at den ikke innfører egne tall. Krever kontroll av svaret og håndtering av feil. |
| Integrasjoner og eksterne tjenester, f.eks. API-er, betaling og e-post | Lav | Bare språkmodell-API. AIS, vær og havnesystemer er ute. |
| Sanntid, samtidighet eller flere brukere som påvirker hverandre | Lav | Én reise om gangen. |
| Filhåndtering, f.eks. opplasting, PDF-lesing og eksport | Lav | Prøvereiser følger med appen. |
| Sikkerhet og personvern | Lav | Simulerte data uten personopplysninger. |

**Hva vanskelighetsgraden betyr for dere:**

- _Middels:_ Et godt balansert valg. Pass på at kjerneflyten – registrer forsinkelse, beregn tre scenarioer, vis sammenligning – blir ferdig og stabil før dere legger på KI-forklaringen og forbedringer i dashbordet.

### Gjennomførbarhet med BMAD og Claude Code

Dere skal planlegge med BMAD (product brief → PRD → arkitektur → epics og stories) og implementere med Claude Code. Vurderingen under tar hensyn til at det må være tid til hele denne flyten, og til testing, retting og README til slutt.

| Spørsmål | Vurdering (OK / risiko / stor risiko) | Kommentar |
|---|---|---|
| **Tid og omfang** – kan v1 realistisk bli ferdig og stabil i løpet av semesteret, med tid til flere iterasjoner? | OK | Én forstyrrelsestype, ett fartøy og tre faste scenarioer er realistisk for én person. |
| **BMAD-flyten** – er briefen konkret nok til at PRD, arkitektur og stories kan lages uten store hull, og blir det overkommelig mange stories? | OK | Seks-stegsflyten passer godt som grunnlag for stories. Kostnadsmodellen er det eneste store hullet. |
| **Egnet for Claude Code** – bruker løsningen en vanlig, godt dokumentert teknologistakk som Claude Code håndterer godt, eller krever den nisjeteknologi, spesialmaskinvare eller mye manuell konfigurasjon? | OK | Teknologistakk er ikke valgt, noe som er riktig i en brief. Et vanlig webdashbord med en enkel backend passer Claude Code godt. |
| **Kontroll på KI-ens arbeid** – kan gruppen selv avgjøre om koden gjør det riktige? Krever domenet kunnskap gruppen ikke har, f.eks. avanserte beregninger eller fagregler, så er det vanskelig å kvalitetssikre. | Risiko | Sjøfartsdomenet er krevende, men forenklede formler kan regnes for hånd. Lag én prøvereise med håndregnede ETA-er og kostnader for alle tre scenarioer før implementeringen. |
| **Testbarhet** – finnes det tydelige regler og forventede resultater som tester kan skrives mot? | OK | Deterministiske beregninger er godt egnet for enhetstester. Kravet om at KI ikke innfører egne tall kan testes ved å sjekke at alle tall i forklaringen finnes i scenarioresultatet. |
| **Kjørbar for sensor** – kan appen kjøres lokalt etter README, uten gruppens nøkler, betalte kontoer eller egen infrastruktur? | Risiko | KI-forklaringen krever nøkkel. Sørg for at resten av appen virker uten, og at README forklarer oppsettet. |
| **Avhengigheter og kostnader** – krever løsningen betalte API-er, f.eks. språkmodeller, og finnes det en plan for kostnad, testmodus eller mock-data? | Risiko | Språkmodell er ikke valgt, og kostnad er ikke omtalt. Velg en løsning med gratisnivå eller lav kostnad, og legg inn mock-modus. |

**Konklusjon om gjennomførbarhet:**

- **Gjennomførbart som beskrevet.**

**Forslag til justering av omfang eller vanskelighetsgrad:**

1. Hold åpent spørsmål om operasjonell risiko utenfor v1 (la det stå i KI-forklaringen), og bruk tiden på at de tre scenarioene og kostnadsmodellen er korrekte og godt testet.
2. Når kjernen virker, kan en enkel utvidelse være at planleggeren selv kan justere farten i et fjerde, egendefinert scenario. Det gir mer funksjonalitet uten ny datamodell.

## Hvorfor product brief er viktig for mappen

Product brief er utgangspunktet for PRD, arkitektur, stories og til slutt koden. Del 1 av mappen vurderes blant annet på om sensor kan følge en sporbar vei fra plan til ferdig app. Den vurderes også på om appen gjør det dere har beskrevet, om den er testet, om den er godt designet, og om den kan kjøres etter README. Et uklart, for stort eller for lite brief gjør alt dette vanskeligere senere. Det er mye enklere å rette nå enn sent i semesteret.

## 1. Gjennomgang av briefens deler

| Del av brief | Status | Kommentar |
|---|---|---|
| Executive Summary – er det klart hva appen er, og hvilket problem den løser? | OK | Klart at VoyageAI viser konsekvensene av en forsinkelse ved anløp og sammenligner tre responser. Briefen er på engelsk, noe som er greit. |
| The Problem – er problemet konkret, med reelle situasjoner og brukere? | OK | Tidsplan, kostnad, kontrakt og utslipp er konkret forklart, og det er ærlig at beskrivelsen ikke er validert med planleggere. |
| The Solution – beskriver løsningen brukeropplevelsen, ikke bare teknologi? | OK | Seks tydelige steg fra brukerens perspektiv. |
| What Makes This Different – er vurderingen ærlig og realistisk? | Juster | Mangler som egen del. Si kort hvordan VoyageAI skiller seg fra å regne i et regneark eller fra kommersielle verktøy for reiseoptimalisering, for eksempel ved forklaringen og sammenligningen side om side. |
| Who This Serves – er primærbrukerne tydelige, og vet vi hva de trenger? | Juster | «Personer som jobber med reiseplanlegging eller maritime operasjoner» er bredt. Beskriv én konkret bruker, for eksempel en operatør i et lite rederi, og hva hun må få gjort når en forsinkelse meldes. |
| Success Criteria – kan kriteriene faktisk sjekkes eller testes? | OK | Kriterium 1–3 er testbare. Kriterium 4–6 handler mer om prosess og kan stå, men suppler gjerne med et kriterium per scenario. |
| Scope – er det klart hva som er med i første versjon, og hva som ikke er det? | OK | Tydelig inn/ut-liste. |
| Vision – henger visjonen sammen med resten uten å blåse opp omfanget? | Juster | Mangler som egen del. Én kort setning om mulige neste steg (flere forstyrrelsestyper, flere fartøy) holder. |

## 2. Utgangspunkt for del 1 av mappen

Punktene følger kriteriene i sensorveiledningen for del 1. Vektene i parentes viser hvor mye hvert kriterium teller i del 1.

| Kriterium i del 1 | Hva briefen bør legge til rette for | Status | Kommentar |
|---|---|---|---|
| **1. Prosess og KI-styring** (30 %) | Brief som er presis nok til at PRD og stories kan bygges direkte på den, slik at krav kan spores fra brief til kode. | OK | Briefen er presis. Lukk de åpne spørsmålene og legg inn addendumet, så er sporbarheten god. |
| **2. Funksjonalitet og omfang** (20 %) | Realistisk omfang for gruppen og semesteret: en tydelig kjerneflyt som kan bli ferdig og stabil, og nok innhold til å vise reell funksjonalitet. | OK | Én tydelig kjerneflyt med reell beregningslogikk. |
| **3. Kvalitetssikring og testing** (15 %) | Suksesskriterier og funksjoner som er konkrete nok til å bli testtilfeller. | Juster | Godt grunnlag, men formlene og en håndregnet prøvereise må på plass før testene kan skrives. |
| **4. Design og brukeropplevelse** (10 %) | Tydelige brukere og brukssituasjoner som designet kan bygges rundt, gjerne med de viktigste skjermbildene eller flytene skissert. | Juster | Dashbordet er beskrevet på overordnet nivå. Skisser reiseoversikten og scenariosammenligningen side om side, og vis tydelig hva som er simulert. |
| **5. Kodekvalitet og arkitektur** (10 %) | Teknologivalg som er begrunnet og ikke mer komplekse enn appen trenger. | OK | Skillet mellom beregning og KI-forklaring gir en naturlig og testbar struktur. |
| **6. README og kjørbarhet** (10 %) | Løsning som andre kan kjøre lokalt uten betalte kontoer, og uten tilgang til gruppens egne tjenester og nøkler. | Juster | Planlegg at appen virker uten KI-nøkkel, med mock-forklaring. |
| **7. Ryddighet i repoet** (5 %) | En plan for hvor hemmeligheter, testdata og dokumentasjon skal ligge. | Juster | Legg addendumet og senere planleggingsdokumenter i en egen mappe, prøvereisene i en datamappe, og bruk `.env.example` for nøkkelen. |

## 3. Neste steg for gruppen

1. Bestem kostnadselementer og formler, skriv dem i briefen eller addendumet, og lag én håndregnet prøvereise med fasit for alle tre scenarioer.
2. Legg `addendum.md` inn i repoet og beskriv mock-modus for KI-forklaringen.
3. Gå videre til PRD med seks-stegsflyten som grunnlag for epics og stories.

Oppdater product brief i repoet når dere har gjort endringene, slik at historikken viser hvordan planen utviklet seg. Det er en del av prosessen sensor ser etter.
