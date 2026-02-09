# CTF-THM-Pickle-Rick
# 🥒 CTF Writeup: Pickle Rick
> **Plataforma:** [TryHackMe](https://tryhackme.com/)
> **Dificuldade:** Fácil  
> **Foco:** Enumeração Web & Escalada de Privilégios

---

## 📝 Resumo do Desafio
Este laboratório simula um servidor web vulnerável onde precisamos ajudar o Rick a encontrar três ingredientes secretos. A jornada passa por reconhecimento de rede, exploração de falhas em aplicações web e má configuração de permissões no Linux.

---

## 🚀 Passo a Passo Técnico

### 1. Reconhecimento (Recon)
O primeiro passo foi identificar os serviços ativos na máquina alvo.
* **Comando:** `nmap -sV <IP_DA_MAQUINA>`
* **Análise:** Identificamos as portas **80 (HTTP)** e **22 (SSH)**. Isso indica que o vetor inicial é web.

### 2. Enumeração e Coleta de Dados
Investiguei o site em busca de falhas humanas ou arquivos esquecidos:
* **Código-Fonte:** Encontrei o usuário `R1ckRul3s` escondido em um comentário HTML.
* **Robots.txt:** Localizei a string `Wubbalubbadubdub` acessando `http://<IP>/robots.txt`.
* **Fuzzing de Diretórios:** Usei o `Gobuster` para encontrar a página oculta `login.php`.

### 3. Exploração (RCE)
Com o login realizado, acessei um painel de execução de comandos.
| Objetivo | Comando Utilizado | Por que? |
| :--- | :--- | :--- |
| **Ingrediente 1** | `less Sup3rS3cretPickl3Ingred1ent.txt` | O comando `cat` estava bloqueado pelo servidor. |
| **Ingrediente 2** | `ls /home/rick` -> `less "/home/rick/second ingredients"` | A flag estava na pasta pessoal do usuário. |

### 4. Escalada de Privilégios (PrivEsc)
Para obter o controle total, verifiquei as permissões de administrador.
* **Comando:** `sudo -l`
* **Vulnerabilidade:** O usuário atual podia executar qualquer comando como root sem senha (`NOPASSWD: ALL`).
* **Ingrediente Final:** `sudo less /root/3rd.txt`

---

## 💡 Lições Aprendidas
1. **Segurança por Obscuridade não funciona:** Comentários em HTML e arquivos robots.txt não devem conter dados sensíveis.
2. **Sanitização de Inputs:** O servidor permitia a execução de comandos via web (RCE), uma falha crítica.
3. **Princípio do Menor Privilégio:** Dar permissão total de `sudo` para um usuário de serviço web (`www-data`) é um erro fatal de configuração.

---
✨ *Writeup para fins educacionais.*
