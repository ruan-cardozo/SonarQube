## **Relatório de Auditoria de Segurança**

### **Ferramenta SAST Utilizada:** 
Foi utilizado a ferramenta **SonarQube**, para a configuração da mesma foi utilizado o docker para baixar a imagem e subir o container:

O docker-compose utilizado para subir o container com o sonarqube:
```yml
version: "3"

services:
  sonarqube:
    image: sonarqube:lts-community
    depends_on:
      - sonar_db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://sonar_db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    ports:
      - "9001:9000"
    volumes:
      - sonarqube_conf:/opt/sonarqube/conf
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
      - sonarqube_temp:/opt/sonarqube/temp

  sonar_db:
    image: postgres:13
    ports:
      - "5433:5432"
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonar
    volumes:
      - sonar_db:/var/lib/postgresql
      - sonar_db_data:/var/lib/postgresql/data

volumes:
  sonarqube_conf:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  sonarqube_temp:
  sonar_db:
  sonar_db_data:
```

Também foi baixado o sonar-scanner, e configurado o arquivo sonar-project.properties, abaixo segue o exemplo do arquivo configurado no repositório do vscode:

```
sonar.projectKey=vscode
sonar.projectName=vscode
sonar.sourceEncoding=UTF-8
sonar.exclusions=**/devcontainer/**,**/configurations/**,**/github/**,**/vscode/**
sonar.sources=src
sonar.tests=test
sonar.inclusions=src/**/*.ts
sonar.test.inclusions=**/test/**
sonar.host.url=http://localhost:9001
sonar.login=sqp_04ddb5f70760865c2a02b54f8a03da36881ed3f4
```

### **Projeto de Software Auditado:**

Fora executado para dois projetos open-source, sendo eles:

- **Nome do Projeto:** node-newrelic.
- **Descrição:** Um pacote que instrumenta seu projeto para monitoramento de desempenho com o New Relic.
- **Link para o Repositório:** [Repositório no Github](https://github.com/newrelic/node-newrelic).
---
- **Nome do Projeto:** vscode.
- **Descrição:** Editor de texto amplamente utilizado pelos desenvolvedores.
- **Link para o Repositório:** [Repositório no Github](https://github.com/microsoft/vscode).

---

## **1. Resumo Executivo**

### **Visão Geral dos Resultados:**

#### node-newrelic
![image](https://github.com/user-attachments/assets/b3cc6bad-90ee-4b01-ae4f-28bf791bb4c8)

No new relic não foi encontrado nenhuma vulnerabilidade pelo scanner do sonar.

#### vscode
![image](https://github.com/user-attachments/assets/fd848447-65f8-424b-9736-243c0e439e91)

No vscode teve 5 vulnerabilidades críticas.

### **Risco Geral do Projeto node-newrelic:** Risco baixo pois não apresenta vulnerabilidades.
### **Risco Geral do Projeto vscode:** Risco médio pois apresenta 5 vulnerabilidades críticas.
---

## **2. Detalhamento das Vulnerabilidades**

### **Vulnerabilidade 1:**

**OWASP A3:2017 - Sensível Exposure de Dados (Sensitive Data Exposure)**

- **Problema:** Sem verificar de onde vem ou para onde vai a mensagem, você pode acabar enviando ou recebendo dados sensíveis para/dos lugares errados.
- **Solução:** Sempre especifique o site certo ao enviar e verifique de onde vem a mensagem ao receber. Isso evita que dados importantes caiam em mãos erradas.

![image](https://github.com/user-attachments/assets/f26929f8-384c-4ac7-bb7c-424dbc90a371)

As outros vulnerabilidades são parecidas com a relatada acima, são problemas com a manipulação de funções que interagem com o DOM(Document Object Model). Abaixo segue os prints de todas as vulnerabilidades encontrados no projeto do vscode:

![image](https://github.com/user-attachments/assets/ef2e1361-0a09-4f7f-8c09-8e75be396d24)


---

## **3. Recomendações**

### **Recomendação para Vulnerabilidade 1:**

Exemplo de código não conforme:

```typescript
var iframe = document.getElementById("testiframe");
iframe.contentWindow.postMessage("secret", "*"); // Noncompliant: * is used
When receiving a message:

window.addEventListener("message", function(event) { // Noncompliant: no checks are done on the origin property.
      console.log(event.data);
 });

```

Solução compatível:

```typescript
var iframe = document.getElementById("testsecureiframe");
iframe.contentWindow.postMessage("hello", "https://secure.example.com"); // Compliant
When receiving a message:

window.addEventListener("message", function(event) {

  if (event.origin !== "http://example.org") // Compliant
    return;

  console.log(event.data)
});
```
---

## **4. Referências**

O própio sonarqube fornece link para documentação da owasp e as principais vulnerabilidades, também fornece possíveis soluções como mostrado acima, essa sugestões foi retirado da plataforma do sonarqube.

---

## **5. Evidências de Auditoria**

- **Projetos** ![Projetos](https://github.com/user-attachments/assets/8c5f0c40-fe62-4a4b-94a1-e1c916a8740e)

---

**Nome do Aluno/Grupo:**  Ruan Cardozo, Guilherme Elias, Guilherme Machado. 
**Data:**  27/08/2024

---
