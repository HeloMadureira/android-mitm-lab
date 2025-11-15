# Laboratório de Análise de Tráfego Android (MITM)

Este projeto documenta o processo de configuração de um laboratório de pentest para interceptar, analisar e descriptografar tráfego de rede (HTTP e HTTPS) de um dispositivo Android, simulando um ataque Man-in-the-Middle (MITM).

---

## 🔬 Meu Laboratório (Ferramentas Utilizadas)

* **Host:** MacBook M1
* **Virtualização:** UTM
* **VM (Atacante):** Kali Linux
* **Dispositivo-Alvo:** Tablet Samsung S6 Lite (Android 10+)
* **Software de Interceptação:** Burp Suite Community
* **Conexão:** ADB (Android Debug Bridge)

---

## 🚧 Processo de Configuração e Desafios

A configuração de um ambiente de interceptação funcional envolveu a superação de vários desafios de rede e segurança.

### 1. Configuração da Rede da VM

* **Desafio:** O Kali Linux, por padrão no UTM, estava em modo de rede "Shared" (NAT). Isso o tornava invisível para o meu tablet, que estava em outra sub-rede.
* **Solução:** Mudei o modo de rede da VM no UTM para **"Bridged" (Modo Ponte)**. Isso permitiu que o Kali recebesse um IP real (ex: `192.168.10.8`) diretamente do roteador, colocando-o na mesma rede que o tablet.

### 2. Diagnóstico de Conexão

* **Desafio:** Mesmo com o proxy configurado, o tablet não conseguia se conectar ao Burp Suite.
* **Solução:** Usei o `adb shell ping 192.168.10.8` para testar a conectividade. Inicialmente, tive `100% packet loss`. Após corrigir a rede da VM (Passo 1), obtive `0% packet loss`, provando que os dispositivos agora podiam se enxergar.

### 3. Configuração do Firewall

* **Desafio:** O `ping` funcionava, mas o tráfego do proxy ainda era bloqueado.
* **Solução:** O firewall do Kali (`ufw`) estava bloqueando conexões de entrada. Utilizei os seguintes comandos para permitir tráfego na porta do Burp Suite e ativar o firewall:
    ```bash
    sudo ufw allow 8080/tcp
    sudo ufw enable
    ```

### 4. Interceptação de HTTPS

* **Desafio:** O tráfego HTTP (`http://testphp.vulnweb.com`) funcionou, mas sites HTTPS (como `google.com`) falhavam com um erro de privacidade.
* **Solução:** Foi necessário fazer o dispositivo Android "confiar" no Burp Suite:
    1.  No navegador do tablet, acessei `http://burp`.
    2.  Baixei o certificado **"CA Certificate"** (`cacert.der`).
    3.  Instalei o certificado no Android através de **Configurações > Segurança > Outras configs. de segurança > Instalar do armaz. > Certificado de CA**.
    4.  **Obstáculo:** O Android exigia um PIN de bloqueio de tela e mostrava um aviso de segurança, que foi contornado clicando em "Instalar assim mesmo".

---

## 🏆 Resultado Final

Após essas configurações, o laboratório estava totalmente operacional. O tráfego HTTP e HTTPS do dispositivo-alvo pôde ser interceptado e analisado com sucesso no Burp Suite.

### Prova de Sucesso:

*Este é o ponto onde você deve colar seus próprios screenshots do Burp Suite mostrando o tráfego HTTP e HTTPS.*

**Exemplo de Tráfego HTTP:**

<img width="676" height="446" alt="Captura de Tela 2025-11-14 às 21 36 52" src="https://github.com/user-attachments/assets/1819cf4a-b126-4eb0-9320-c784f1a1b2c9" />



**Exemplo de Tráfego HTTPS:**


<img width="676" height="446" alt="Captura de Tela 2025-11-14 às 21 37 22" src="https://github.com/user-attachments/assets/4ccb42ce-b2ee-4bee-80f8-f9574323ebea" />

