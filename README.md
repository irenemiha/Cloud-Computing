# <font color="blue">Curs 1 - Context</font>

## Ce este un Cloud?
Cloud-ul presupune:
- Abstractizarea infrastructurii: ascunderea complexității.
- Externalizarea acesteia către un furnizor (provider), care se ocupă de mentenanță și resurse hardware.

## Modele de Cloud Computing
### IaaS – Infrastructure as a Service
- Primești hardware sau mașini virtuale (VM-uri).
- Trebuie să configurezi totul singur (OS, aplicații).

**Avantaje:**
- Control complet asupra aplicațiilor.
- Inițial mai ieftin decât o infrastructură locală (on-prem).
- Flexibil și potrivit pentru majoritatea nevoilor.

**Dezavantaje:**
- Necesită expertiză umană (costuri mai mari).
- Posibilă dependență de furnizor (vendor lock-in).
- Risc de probleme de securitate (resurse partajate).

**Exemple de IaaS:**
- Amazon EC2, Google Compute Engine, Microsoft Azure VMs.

### PaaS – Platform as a Service
- Primești o platformă completă cu API și framework.
- Doar faci deployment (implementarea aplicației).

**Avantaje:**
- Nu te ocupi de mentenanța platformei.
- Deployment independent de hardware.

**Dezavantaje:**
- Mai scump decât IaaS, costul crește pe măsură ce folosești mai multe funcții.
- Nu ai acces la nivel jos (low-level).
- Securitatea este gestionată de provider – trebuie să ai încredere.

**Exemple de PaaS:**
- Heroku, Google App Engine, Azure App Service.

### SaaS – Software as a Service
- Primești acces direct la o aplicație deja funcțională.

**Avantaje:**
- Cel mai ușor de folosit.

**Dezavantaje:**
- Cel mai scump model.
- Control redus – ești dependent total de furnizor.

**Exemple de SaaS:**
- Gmail, Google Docs, Microsoft Office 365, Dropbox.

## Cum alegem între VM, Container sau Native?
Când vrem să găzduim o aplicație, alegerea între:
- rulare nativă (pe sistemul de operare direct)
- containere (ex. Docker)
- VM-uri (ex. prin IaaS)

...depinde de factori precum:
- Cine gestionează aplicația?
- Cum va fi accesată?
- Ce performanță este necesară?
- Cum se va gestiona stocarea?
- Ce tip de rețea este necesară?
- Cerințele de securitate ale aplicației?
<br>
<br>
<br>

# <font color="blue">Curs 2 - Volumes, Networking and Compose</font>

## De ce containere (Docker)?
Containerele oferă avantaje clare față de mașinile virtuale:
- Sunt lightweight – consumă mai puține resurse.
- Asigură un mediu coerent pe toate sistemele în care se face deploy:
    - Imaginile sunt statice (nu se schimbă odată cu rularea).
    - Se construiesc în pași repetabili și clari.
- Mai ușor de creat decât un VM, deci mai eficient pentru dezvoltatori.

## Conectarea la un container
**Local**
- Se folosește comanda:
```
docker exec -it [nume_container] /bin/bash
```
pentru a intra într-un container și a-l controla direct prin CLI.

**Remote**
- Este necesar un port expus către exterior (ex. cu -p 8080:80).

## Networking în Docker
**Comportament implicit (default)**
- Docker creează o rețea proprie (bridge network) pentru containere.
- Containerele comunicate în această rețea implicită.
- Este permisă comunicarea din interior spre exterior, nu invers fără configurare.

**Crearea și gestionarea rețelelor**
- Comenzi utile:
```
docker network create [nume_rețea]
docker run --network [rețea] ...
docker network ls  # listează rețelele
docker run -p 8080:80 ...  # expune port
```
Tipuri de rețele în Docker
- Bridge: rețea izolată pentru containere, ideală pentru majoritatea cazurilor.
- Host: containerul folosește direct rețeaua gazdei (fără izolare).
- None: fără rețea – containerul este complet izolat.

## Volumes în Docker
**De ce avem nevoie de volume?**
- Containerele sunt efemere – dacă sunt șterse, datele din interior dispar.
- Exemplu: după un upgrade de serviciu, datele anterioare se pierd dacă nu folosim volume.

**Tipuri de volume**
#### 1. Bind Mounting
- Se montează un director local din gazdă într-o cale din container.
- Ex:
    ```
    docker run -v /cale/pe/gazdă:/cale/container ...
    ```
- Avantaje:
    - Acces facil din sistemul gazdă.
    - Ușor de făcut backup.
- Dezavantaje:
    - Gestionare manuală.
    - Securitatea este responsabilitatea utilizatorului.
#### 2. Data Volumes (gestionate de Docker)
- Docker se ocupă de persistarea datelor.
- Avantaje:
    - Abstractizează complexitatea.
    - Poate fi migrat ușor.
- Dezavantaje:
    - Necesită configurare și curățare (cleanup).

## Docker Compose
- În loc să construim un container monolitic, putem împărți aplicația în mai multe containere specializate:
    - Ex: un container pentru backend, unul pentru frontend, altul pentru baza de date.
- Acest lucru permite:
    - Modularitate.
    - Scalabilitate.
    - Management mai clar și separat al componentelor.
<br>
<br>
<br>

# <font color="blue">Curs 3 - Kubernetes</font>

## Trecerea la medii de producție
- Până acum: folosire simplă a containerelor (local, testare, docker-compose).
- Limitări: Compose merge doar pe un nod, nu e scalabil.
- În producție avem nevoie de uptime, scalare și fiabilitate.

## Cerințe pentru producție
- Automatizare: redresare automată, scalare după trafic, gestionare certificate.
- Upgrade & Rollback: upgrade treptat, blue-green deployment.
- GitOps/CI-CD: deployment-uri automate, patching rapid, testare automată.

## Kubernetes (K8s)
- Orchestrator de containere dezvoltat de Google.
- Rulează pe mai multe noduri, extensibil.
- Devine standard industrial.

## Arhitectură Kubernetes
**Master Node**
- **API Server:** primește comenzi.
- **etcd:** stochează starea clusterului.
- **Controller:** menține starea dorită.
- **Scheduler:** alocă workload pe noduri.

**Worker Node**
- Rulează containerele.
- Include kubelet și CRI (ex. containerd, cri-o).

## Obiecte și comenzi de bază
- **Verbe:** get, create, delete, describe, logs
- **Namespace:** izolare între echipe.
- **Pod:** grup de containere.
- **ReplicaSet:** menține nr. de poduri activ.
- **Deployment:** definește rollout-ul aplicației.

## Exemple practice
- Creare pod:
    ```
    kubectl run -it --image=alpine ping-test -- ping 127.0.0.1 -c 10
    ```
- Creare deployment:
    ```
    kubectl create deployment pingpong --image=alpine -- ping 127.0.0.1
    ```
- Verificare:
    ```
    kubectl get all
    kubectl describe deployment pingpong
    ```
- Testare ReplicaSet:
    ```
    kubectl delete pod [nume]
    # ReplicaSet creează altul automat
    ```
<br>
<br>
<br>

# <font color="blue">Curs 4 - Ingres and scaling</font>
## Recapitulare Kubernetes
- Kubernetes este un sistem de gestionare automată a aplicațiilor containerizate.
- Permite pornirea, conectarea, limitarea, scalarea și distribuirea containerelor pe mai multe noduri.

## Deployment-uri
- Reprezintă aplicații rulate în cluster.
- Pot fi create manual (ex: ```kubectl create deployment```) sau, preferabil, prin fișiere manifest.
- Avantajele fișierelor manifest: Sunt repozitabile (Git), permit blame, rollback, cherrypick.
- Taxonomia unui fișier manifest (ex. nextcloud.yaml):
    - ```apiVersion```: Versiunea API-ului Kubernetes.
    - ```kind```: Tipul obiectului (ex: Deployment).
    - ```metadata```: Date despre date, incluzând name (numele deployment-ului) și labels (etichete pentru selectarea obiectelor).
    - ```spec```: Specificațiile deployment-ului, incluzând **replicas** (numărul de instanțe), **selector** (pentru a identifica pod-urile asociate), **template** (șablonul pentru pod-uri, cu metadata și spec pentru containere).
    - ```containers```: Definește containerul/containerele (ex: name, image).

## Aplicarea Manifestelor
- Generează un obiect nou, nu aplică modificări dacă obiectul există.
    ```
    kubectl create -f <fișier>
    ```
- Generează obiecte și aplică modificări asupra obiectelor existente.
    ```
    kubectl apply -f <fișier>
    ```
## Conectarea la un Deployment (Servicii)
- Problema IP-urilor efemere: IP-urile podurilor se schimbă la restart sau redeploy.
- Soluția: Servicii (Services): Obiecte care expun un set de poduri către cluster sau exterior, oferind un punct de acces static.
- Crearea unui serviciu: Ex: ```kubectl expose deployment nextcloud --port 80```.

## Tipuri de Servicii
**ClusterIP**
- Expune serviciul doar intern, în cadrul clusterului Kubernetes.
- Are o adresă IP statică (cluster-ip).
- Conectivitatea este posibilă între poduri și între poduri și nodul fizic.
- Conectarea se poate face prin IP-ul static sau prin numele serviciului.

**NodePort**
- Expune serviciul în afara clusterului.
- Deschide un port pe toate nodurile clusterului.
- Conectarea se face folosind adresa IP a nodului și portul alocat (ex: ```kubectl get nodes -o wide```).
- Util pentru infrastructuri interne sau când nu există adrese IP publice alocabile.

**LoadBalancer**
- Disponibil în infrastructurile cloud.
- Alocă o adresă IP publică (ExternalIP) accesibilă din afara clusterului.
- Redirectează conexiunile către serviciul Kubernetes.

## Kubernetes Ingress
- Folosit pentru a gestiona traficul HTTP/HTTPS către servicii web multiple printr-o singură adresă IP publică.
- Un Ingress Controller (ex: bazat pe **Nginx**) își alocă o adresă IP publică și preia conexiunile.
- Ingress este obiectul care definește rutele de la Ingress Controller către serviciile corecte, pe baza hostname-ului.
- Poate fi integrat cu cert-manager pentru generarea automată a certificatelor.
- Se folosește de un serviciu deja definit în cluster.

## Scalarea Automată a Deployment-urilor
- Scalare manuală: ```kubectl scale deployment <nume> --replicas <număr replici>``` sau prin câmpul replicas în manifest.
- Alocarea Resurselor (în manifest):
    - ```resources.limits```: Impune limite de alocare (ex: cpu: 200m).
    - ```resources.requests```: Cere o alocare minimă de resurse (ex: cpu: 100m).
    - Limitele sunt impuse de **kubelet** folosind **cgroups** și sunt folosite în procesul de programare a resurselor.
- ```kubectl top pods```: Afișează resursele folosite de poduri.
- **HorizontalPodAutoscaler (HPA)**:
    - Obiect care gestionează și scalează numărul de replici în funcție de condiții.
    - Monitorizează utilizarea CPU-ului (sau alte metrici).
    - Definește **minReplicas** și **maxReplicas**.
    - Scalează în funcție de utilizarea medie a CPU-urilor pe toate podurile din deployment.
    - Formula de scalare: ```ceil(currentReplicas * (currentCPUValue desiredCPUValue)).```

<br>
<br>
<br>

# <font color="blue">Curs 5 - Izolarea în Kubernetes</font>

## Introducere în Izolarea și Securitatea Aplicațiilor în Kubernetes:
- Kubernetes este un orchestrator de containere pentru crearea, scalarea și managementul aplicațiilor.
- Izolarea și securizarea aplicațiilor sunt esențiale într-un cluster.
- Implicit, în Kubernetes, majoritatea resurselor sunt accesibile între ele fără restricții stricte de comunicare sau consum de resurse.
- Responsabilitatea dezvoltatorului/sysadmin-ului este să asigure izolarea și securitatea.

## Mecanisme de Izolare în Kubernetes:
- ```cgroups```: Controlul resurselor (CPU, memorie, I/O) la nivel de container.
- ```namespaces```: Izolarea logică a obiectelor (poduri, servicii, deployment-uri, config maps).
- ```network policies```: Filtrarea traficului de rețea.
- ```RBAC (Role-based access-control)```: Controlul accesului la resurse bazat pe roluri.

## cgroups - Limitarea Resurselor la Nivel de Container:
- Mecanism de control al resurselor "primitive" la nivel de container.
- Previne consumul excesiv de resurse de către un container.
- Implementat la nivel de kernel Linux, expus în K8s prin runtime-ul de containere.
- Fiecare container are un cgroup asociat.
- În manifestul deployment-ului:
    - ```resources.requests```: Specifica resursele minime garantate de K8s.
    - ```resources.limits```: Specifica resursele maxime pe care podul le poate folosi.
- ```crictl```: Un tool pentru a inspecta containerele care rulează în cluster.

## Namespaces - Separarea Logică a Obiectelor:
- Asigură separarea logică între obiecte (poduri, servicii, deployment-uri etc.).
- Permit organizarea mediilor (dezvoltare, testare, producție) sau izolarea echipelor/clienților.
- Fiecare namespace poate avea propriile resurse și politici de acces.
- **Cazuri de utilizare**: Izolarea resurselor între medii, între componentele unei aplicații (frontend, backend), sau pentru multi-tenancy.
- Comenzi:
    - ```kubectl get namespaces```: Afișează namespace-urile existente.
    - ```kubectl config set-context --current --namespace=<namespace-name>```: Schimbă namespace-ul curent.
- Conectarea între poduri din namespace-uri diferite:
    - Implicit, comunicarea este permisă.
    - Adresarea se face folosind ```[nume_service].[nume_namespace].svc.cluster.local```.
    - ```network policies``` pot fi folosite pentru a limita accesul.

## Network Policies - Izolarea Traficului de Rețea:
- Mecanism de izolare a traficului de rețea în K8s.
- Cazuri de utilizare: Izolarea traficului între namespace-uri, între poduri, restricționarea accesului extern.
- Implicit, fără **NetworkPolicy** aplicat, tot traficul este permis.
- Odată ce există o politică aplicată, traficul este blocat implicit, cu excepția celor permise explicit.
- Componentele unui NetworkPolicy:
    - ```podSelector```: Defineste care poduri sunt afectate.
    - ```policyTypes```: Ingress (trafic de intrare), Egress (trafic de ieșire) sau ambele.
    - ```ingress```: Reguli pentru traficul de intrare.
    - ```egress```: Reguli pentru traficul de ieșire.
- Un selector gol (```{}```) înseamnă că toate podurile/regulile sunt afectate.
- **Lipsa regulilor de ingress/egress** înseamnă restricționarea totală a traficului respectiv.
- **NetworkPolicies** sunt aditive: dacă mai multe politici sunt aplicate, regulile se combină, iar traficul este permis dacă cel puțin o politică îl permite.
- Clasificarea traficului de rețea:
    - **North-South**: Între poduri și exteriorul clusterului.
    - **East-West**: Între poduri din același namespace sau din namespace-uri diferite.

## Role Based Access Control (RBAC) - Controlul Accesului:
- Mecanism pentru definirea drepturilor de acces pentru utilizatori și servicii, bazat pe roluri.
- **Cazuri de utilizare**: Definirea rolurilor clare, implementarea principiului *least privilege*, restricționarea accesului pentru service accounts.
- Componentele unui RBAC policy:
    - ```Role``` sau ```ClusterRole```: Definește ce acțiuni (verbs) pot fi făcute pe ce resurse (resources).
        - ```Role```: Se aplică într-un singur namespace.
        - ```ClusterRole```: Se aplică la nivel de cluster (toate namespace-urile).
    - ```RoleBinding``` sau ```ClusterRoleBinding```: Atașează un rol unui ServiceAccount sau utilizator.
        - ```RoleBinding```: Leagă un Role (sau ClusterRole) de un utilizator într-un namespace.
        - ```ClusterRoleBinding```: Leagă un ClusterRole de un utilizator la nivel de cluster.
    - ```ServiceAccount```:
        - Folosit de podurile care rulează în cluster pentru a interacționa cu API-ul Kubernetes.
        - Definește identitatea aplicațiilor (podurilor).
        - Implicit, fiecare pod primește un ServiceAccount cu acces limitat la API.
        - Include un token JWT montat în container.
<br>
<br>
<br>

# <font color="blue">Curs 6 - Cloud Computing Fundamentals</font>

## Concepte Fundamentale Cloud:

**Modele Cloud:**
- **Public Cloud:** Deținut de furnizorul de servicii cloud, oferă resurse și servicii către mai multe organizații și utilizatori, accesat prin internet.
- **Private Cloud (On-prem):** Organizațiile își creează un mediu cloud în propriul datacenter; nu oferă acces utilizatorilor din afara organizației.
- **Hybrid Cloud:** Combină cloud-uri publice și private, permițând aplicațiilor să ruleze în locația cea mai potrivită.

**Tipuri de Servicii Cloud:**
- **IaaS (Infrastructure as a Service):** Construirea infrastructurii IT prin închirierea de servere, mașini virtuale, stocare, rețele și sisteme de operare de la un furnizor cloud. Utilizatorul configurează și gestionează hardware-ul. Este cel mai flexibil serviciu. (Ex: Azure VMs, Azure Networking, Azure Storage).
- **PaaS (Platform as a Service):** Furnizează un mediu pentru construirea, testarea și deploy-ul de aplicații. (Nu sunt detalii suplimentare în fragmentele disponibile, dar implică mai puțină gestionare hardware).
- **SaaS (Software as a Service):** 

## Gestionarea Resurselor Cloud:

**Regiuni și Zone de Disponibilitate:** (Menționat în agendă, dar fără detalii în fragmente).
**Identity and Governance (Identitate și Guvernanță):**
- **Azure Active Directory (AAD):** Serviciu de identitate.
- **Role-Based Access Control (RBAC):**
    - Gestionare fină a accesului.
    - Permite segregarea sarcinilor în echipă și acordarea accesului minim necesar utilizatorilor.
    - Controlul accesului la portalul Azure și la resurse.
- **Resource Group (Grup de Resurse)**
- **Azure Subscription (Abonament Azure)** 

## Instrumente de Management Cloud:
- **Azure Resource Manager (ARM):**
    - Oferă un strat de management pentru crearea, actualizarea și ștergerea resurselor în abonamentul Azure.
    - Suportă ```Infrastructure as Code (IaC)``` (Ex: crearea unui VM).
- **CLI (Command Line Interface):**
- **API**
- **Terraform** 
<br>
<br>
<br>

# <font color="blue">Curs 7 - Pipeline CI/CD</font>

## Ce este CI/CD?
- Acronim pentru **Continuous Integration / Continuous Delivery** (sau Continuous Deployment).
- **Un set de practici și instrumente** ce ajută dezvoltatorii să livreze cod stabil și reproductibil.
- **Scopul:** Automatizarea procesului de livrare a codului, de la testare până la deployment.
- **Input:** O nouă versiune a codului (ex: commit, PR).
- **Output:** Un artefact (ex: imagine Docker, pachet nou) și/sau un nou deployment.

## Pipeline-ul CI/CD
- **Un set de acțiuni** executate ca urmare a unei modificări a codului sursă.
- Acțiunile sunt definite într-un fișier de configurare (ex: .gitlab-ci.yml, .github/workflows/main.yml).
- Executat pe un runner (VM sau container).

## Relația CI/CD cu Release-ul
- **CI/CD** este componenta dintr-un proiect care *duce* codul în producție.
- Un *release în producție* este versiunea finală a aplicației livrată utilizatorilor reali.

## Medii de Dezvoltare (înainte de Release)
- **Development (dev):** Mediu de dezvoltare și testare preliminară.
- **Staging**: Mediu care simulează producția, pentru teste finale.
- **Production (prod)**: Mediul real unde rulează aplicația pentru utilizatorii finali.

## Continuous Integration (CI)
- Practica de a integra frecvent modificările de cod.
- Fiecare integrare este verificată printr-o build automatizată și teste.
- **Scop:** Identificarea rapidă a erorilor de integrare.
- **Exemple de acțiuni CI:** Compile, testare unitară, testare de integrare, analize de cod, build al imaginii Docker.

## Continuous Delivery (CD)
- Extinde CI, asigurând că soft-ul este întotdeauna într-o stare "deployabilă".
- Aplicația este pregătită pentru release oricând, dar deployment-ul în producție este manual.
- **Exemple de acțiuni CD:** Crearea unui pachet, push într-un registry, deploy într-un mediu de staging, teste de acceptanță.

## Continuous Deployment (CD)
- Pasul final al CD.
- Fiecare schimbare care trece toate testele automate este automat deploiată în producție.
- Necesită un nivel înalt de încredere în pipeline-ul automat.
- Reduce *timpul până la piață* (time-to-market).

## Strategii de Deployment în Kubernetes
**Recreate**
- Oprire toate instanțele vechi.
- Pornire toate instanțele noi.
- **Avantaje:** Simplu, downtime scurt.
- **Dezavantaje:** Downtime semnificativ, lipsa rollback-ului rapid.
- Implicit strategy.type: Recreate în Kubernetes.

**Rolling Update (sau Rolling Deployment)**
- Implicit în Kubernetes (dacă nu se specifică Recreate).
- Oprește treptat instanțele vechi și pornește instanțe noi.
- Nu există downtime complet.
- **Avantaje:** Fără downtime, rollback posibil.
- **Dezavantaje:** Problemele pot afecta toți utilizatorii, rollback lent, trafic redirecționat către versiuni multiple.
- Controlat prin maxUnavailable și maxSurge.

**Blue/Green Deployment**
- Două medii de producție identice: "Blue" (versiunea curentă) și "Green" (noua versiune).
- Noua versiune este deploiată pe mediul "Green" separat.
- Traficul este comutat instantaneu de la "Blue" la "Green" printr-un Load Balancer sau Ingress.
- **Avantaje:** Zero downtime, rollback instant, izolare completă a noii versiuni.
- **Dezavantaje:** Costuri mai mari (dublă infrastructură), complexitate.
- Implementare în K8s: Două deployment-uri și un Service/Ingress.

**Canary Deployment (sau Rollout Deployment)**
- Lansează noua versiune (canary) către un mic procent din utilizatori.
- Monitorizează comportamentul (erori, performanță).
- Dacă totul e OK, se crește treptat procentul de trafic către noua versiune.
- **Avantaje:** Reduce riscul, testează în producție, feedback timpuriu.
- **Dezavantaje:** Complexitate, necesită monitorizare robustă, rollback-ul necesită atenție.
- Implementare în K8s: Poate folosi Argo Rollouts, Nginx Ingress Controller sau Istio.
- **Argo Rollouts:** Un controller Kubernetes care extinde deployment-urile cu strategii avansate (Canary, Blue/Green, A/B testing).
    - Definește setWeight (procent de trafic) și pause (timp de așteptare).

## Rollback
- Procesul de revenire la o versiune anterioară a aplicației.
- Necesită un sistem de versionare (ex: Git, Docker registry).
- Este crucial pentru a reduce downtime-ul și a asigura stabilitatea.
- **Mecanisme de Rollback:**
    - Schimbarea traficului înapoi la deployment-ul vechi (Blue/Green).
    - Folosind un tool de CI/CD pentru rollback automat.
<br>
<br>
<br>

# <font color="blue">Curs 8 - Data Pipelines</font>

## Ce sunt Data Pipelines?
- Datele în acest context: Se referă majoritar la evenimente (ex: tranzacții online, evenimente de login, loguri de securitate, date de la senzori).
- **Definiție:** Un flux de procesare a datelor, de la ingestie până la analiză sau stocare.
- **Scop:** Agregarea, procesarea și consumul informației.

## Cazuri de Utilizare (Use Cases):
- Managementul securității
- Gestiunea tranzacțiilor în e-commerce
- Parsarea informațiilor de la senzori (IoT)
- Managementul inventarului

## Conectarea la Microservicii:
- Cursul se concentrează pe soluții bazate pe microservicii.
- Microserviciile sunt servicii de sine stătătoare care comunică între ele.
- **Avantaje microservicii:**
    - Scalare individuală a componentelor.
    - Update-uri selective.
    - Proces de dezvoltare înlesnit (modificarea unei componente nu afectează întreaga soluție).

## Necesitatea unui Sistem de Mesagerie (Message Broker):
- În arhitecturile cu microservicii, comunicarea directă între servicii (RPC, HTTP) poate deveni complexă.
- Necesitatea unui message broker (coadă de mesaje) pentru a gestiona comunicarea asincronă.
- **Avantaje message broker:**
    - **Scalabilitate:** Permite adăugarea de noi microservicii (consumatori/producători) fără a afecta sistemul existent.
    - **Decuplare:** Serviciile nu trebuie să știe despre existența sau locația exactă a altor servicii.
    - **Reziliență:** Dacă un serviciu este oprit, mesajele pot fi stocate și procesate ulterior.
    - **Replay:** Posibilitatea de a re-procesa mesaje vechi.

## Apache Kafka - Un Sistem de Streaming de Evenimente:
- O platformă distribuită pentru streaming de evenimente.
- Arhitectură:
    - **Producători (Producers)**: Publică mesaje într-un topic.
    - **Consumatori (Consumers):** Se abonează la topicuri pentru a primi mesaje.
    - **Topicuri (Topics):** Categorii de mesaje la care producătorii publică și consumatorii se abonează.
    - **Partiții (Partitions):** Un topic poate fi împărțit în mai multe partiții, fiecare fiind o secvență ordonată de mesaje. Permite scalare orizontală și redundanță.
    - **Brokeri (Brokers):** Servere Kafka care stochează topicurile și partițiile.
    - **ZooKeeper (sau KRaft):** Coordonator pentru brokeri și gestionarea metadatelor clusterului Kafka.

## Caracteristici Cheie Kafka:
- **Stocare distribuită și persistentă:** Mesajele sunt stocate pe disc și replicate pe mai mulți brokeri.
- **Performanță ridicată:** Conceput pentru throughput mare.
- **Ordonare:** Mesajele dintr-o partiție sunt întotdeauna ordonate.
- **Replay:** Posibilitatea de a re-consuma mesaje de la un offset anterior.
- **Redundanță:** Asigurată prin replicarea partițiilor.

## Organizarea Consumatorilor în Kafka:
- Consumatorii se abonează la topicuri.
- Grupuri de Consumatori (Consumer Groups): Permite mai multor consumatori să proceseze același pool de evenimente fără suprapuneri.
- Fiecare consumator dintr-un grup primește evenimente unice din coadă.
- Numărul de consumatori dintr-un grup este limitat de numărul de partiții din topic. Dacă există mai mulți consumatori decât partiții, consumatorii suplimentari vor fi inactivi (pentru redundanță).
<br>
<br>
<br>

# <font color="blue">Curs 9 - Persistence in Containers</font>

## Introducere - Limitări neacoperite până acum:
- Până acum, cursurile s-au concentrat pe partea de "compute" (rularea serviciilor, disponibilitate, acces, limitări de resurse, deployment-uri).
- O limitare majoră nediscutată este persistența datelor. Datele din containere sunt efemere; ele se pierd la oprirea sau ștergerea containerului/podului.

## Volume în Kubernetes (Concept de bază):
- În Kubernetes, un "Volume" (fără prefixul "Persistent") este o metodă de a partaja fișiere între containere din același pod și pe același host.
- Nu oferă persistență reală a datelor în cazul în care podul este mutat pe alt nod sau recreat.
- Exemplu: Partajarea conținutului clonat de Git între un container ```git``` și un container ```nginx``` în același pod, folosind un volum ```emptyDir```. ```emptyDir``` este un volum temporar care este creat la inițializarea podului și șters când podul este eliminat.

## Problema Persistenței Datelor:
- Cum facem ca datele generate de aplicațiile din containere să supraviețuiască opririi/recreării podurilor?
- Cum asigurăm că datele sunt disponibile chiar dacă podul este mutat pe un alt nod?

## Persistent Volumes (PV) și Persistent Volume Claims (PVC):
**Persistent Volume (PV):**
- O bucată de stocare reală în cluster (ex: un disc pe cloud, un NFS share).
- Este o resursă la nivel de cluster, gestionată de administratorul de Kubernetes.
- Definește proprietățile stocării (capacitate, mod de acces, ```storageClassName```).
- Exemple de tipuri de PV-uri: ```hostPath``` (pentru teste, nu în producție), ```nfs```, cloud-specific (Azure Disk, AWS EBS).
- ```reclaimPolicy```: Definește ce se întâmplă cu datele după ce PV-ul nu mai este folosit (ex: ```Retain``` - păstrează datele; ```Delete``` - șterge datele).

**Persistent Volume Claim (PVC):**
- O cerere de stocare de către un dezvoltator/aplicație.
- Este o resursă la nivel de ```namespace```.
- Definește cerințele aplicației (capacitate, mod de acces, ```storageClassName```).
- Aplicația cere un PVC, iar Kubernetes găsește un PV care să corespundă cerințelor și le *leagă* (bind).

**Flow:** Administratorul creează PV-uri -> Utilizatorul creează PVC-uri -> Kubernetes leagă PVC-uri la PV-uri -> Podurile folosesc PVC-uri.

## Storage Classes:
- O resursă Kubernetes care abstractizează tipurile de stocare disponibile.
- Permite administratorilor să definească diferite "clase" de stocare (ex: ```standard```, ```premium```, ```fast```).
- Dezvoltatorii pot cere un PVC specificând un ```storageClassName```, fără să știe detalii despre infrastructura de stocare subiacentă.
- Activarea **Dynamic Provisioning:** Când un PVC este creat și nu există un PV disponibil, Storage Class-ul poate provisiona automat un nou PV.

## Utilizarea unui PVC într-un Pod:
- Un pod poate monta un PVC ca un volum.
- Aceasta permite ca datele să persiste chiar dacă podul este șters sau mutat pe un alt nod, deoarece volumul este legat de un PV, care la rândul său este legat de stocarea fizică.
- ```volumeMounts```: Definește calea în container unde va fi montat volumul.

## Moduri de Acces (Access Modes):
- ```ReadWriteOnce (RWO)```: Volumul poate fi montat ca read-write de un singur nod.
- ```ReadOnlyMany (ROX)```: Volumul poate fi montat ca read-only de mai multe noduri.
- ```ReadWriteMany (RWX)```: Volumul poate fi montat ca read-write de mai multe noduri. (Acest lucru necesită o soluție de stocare care să suporte RWX, cum ar fi NFS).

## StatefulSets:
- Obiect Kubernetes folosit pentru gestionarea aplicațiilor ```stateful``` (care au nevoie de date persistente și identitate unică).
- Asigură o identitate de rețea stabilă și stocare persistentă pentru fiecare pod.
- Este ideal pentru baze de date, sisteme de mesagerie (ex: Kafka) și alte aplicații care necesită persistență de date.
<br>
<br>
<br>

# <font color="blue">Curs 10 - Openshift</font>

## OpenShift vs. Kubernetes:
- OpenShift este o distribuție de Kubernetes vândută de Red Hat, dar există și o variantă Open Source cu suport comunitar.
- Simplifică administrarea clusterelor Kubernetes prin abstractizarea unor complexități precum:
    - Deployment-ul mașinilor virtuale în OpenStack.
    - Integrare facilă cu OIDC (OpenID Connect).
    - Operatori custom.
    - Integrarea cu Ingress-uri.
    - Managementul proiectelor.
- Oferă dashboard-uri și automatizări pentru o vizibilitate mai bună.
- Include operatori pentru funcționalități extra, plugin-uri și suport pentru noi tipuri de resurse, unele exclusiv pentru clienții Red Hat.

## Operatori în Kubernetes:
- Un tip de plugin care generează obiecte noi și adaugă funcționalități în Kubernetes.
- Funcționalitățile variază în funcție de caz de utilizare.
- Cazuri de utilizare relevante:
    - Instalarea și utilizarea driverelor pentru GPU-uri.
    - Identificarea capabilităților hardware și expunerea lor către containere.
    - Generarea automată de workspace-uri și IDE-uri.
    - Verificări automate de securitate.
- Avantaje: Reduc complexitatea de administrare și deployment, oferă funcționalități la nivel de cluster accesibile în funcție de permisiuni.

## Batch Jobs:
- O formă de programare a muncii sub formă de joburi distincte.
- Cazuri de utilizare: Procesarea datelor, raportare, antrenarea modelelor de Machine Learning.
- Tipuri de execuție:
    - Serial: Un job este executat o singură dată.
    - Parallel: Jobul este împărțit în mai multe task-uri care rulează în paralel.
    - Work Queue: Task-urile sunt preluate dintr-o coadă de lucru.
- Obiectul **Job** în Kubernetes:
    - Definește un pod care se va opri după finalizarea cu succes a procesului.
    - Poate rula o singură dată sau de mai multe ori.
    - Monitorizează execuția podurilor.
    - ```restartPolicy```: Implicit Never, poate fi ```OnFailure``` (pentru a reîncerca la eroare).
    - ```backoffLimit```: Numărul maxim de reîncercări.
    - ```completions```: Numărul de execuții reușite ale jobului.
    - ```parallelism```: Numărul de poduri care pot rula simultan.
- Obiectul **CronJob** în Kubernetes:
    - Rulează joburi la intervale regulate (programate).
    - Definește un Job care va fi executat conform unui program cron.
    - ```concurrencyPolicy```: Reguli pentru tratarea execuțiilor concurente (ex: Forbid, Replace, Allow).
    - ```successfulJobsHistoryLimit``` / ```failedJobsHistoryLimit```: Limitează numărul de joburi păstrate în istoric.

## Resurse GPU în Kubernetes:
- Pentru a folosi GPU-uri în Kubernetes, trebuie să existe un Operator (ex: NVIDIA GPU Operator).
- NVIDIA oferă mai multe abordări pentru a folosi GPU-urile:
    - Device Plugins: Permit podurilor să consume GPU-uri ca resurse.
    - **NVIDIA vGPU (Virtual GPUs):**
        - GPU-uri virtuale pasate direct mașinilor virtuale sau proceselor.
        - Izolare completă între workload-uri.
        - Necesită împărțire statică, hardcodată de utilizatori.
        - Slice-urile sunt predefinite de Nvidia.
        - **Dezavantaj:** necesită input administrator pentru modificarea slice-urilor.
    - **MPS (Multi-process Service):**
        - Permite rularea concurentă a joburilor pe același GPU.
        - Joburile împart sloturi de procesor și memorie.
        - Pot apărea vulnerabilități de securitate (side channels).
        - Model gândit pentru un mod de lucru cooperativ.
    - **Time Slices:**
        - Un proces primește o cuantă de timp (acces la resurse complete GPU) și apoi cedează.
        - Duplic la multe schimbări de context și overhead.
        - În Kubernetes, se poate specifica o prioritate pentru a aloca slice-uri de timp mai mari workload-urilor responsive.
        - **Avantaj:** izolare garantată față de alte procese (în timpul slice-ului).
