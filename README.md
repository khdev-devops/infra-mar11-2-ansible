# Installera och konfigurera Apache med Ansible

## Meddelande från kunden

Vi har försökt installlera och konfigurera en Apache-webbserver på vår EC2-instans med hjälpa av Ansible, men något är fel. Vi kan inte se vår webbplats. Kan du hjälpa oss att felsöka och fixa vår Ansible-konfiguration?

Målet är att:
- Installera och starta Apache på en EC2-instans
- Se till att webbservern körs automatiskt efter en omstart
- Servern visar en webbsida på instansens IP-adress

Vi har redan satt upp EC2-instansen med OpenTofu, så ni kan köra Ansible från AWS CloudShell och låta Ansible få access till EC2-instansen via SSH.

Lycka till!  
**– Kundens DevOps Team**

---

## Uppgift: Debugga och fixa Ansible-konfigurationen

1. Klona repo och navigera till projektmappen:
   ```bash
   git clone https://github.com/khdev-devops/infra-mar11-2-ansible
   ```
   ```bash
   cd infra-mar11-2-ansible
   ```

2. Installera Ansible i AWS CloudShell
   Detta kan behöva göras då och då eftersom CloudShell inte sparar installationer av paker och mjukvara:
   ```bash
   ./install_ansible.sh
   ```
   Kontrollera att Ansible är installerat:
   ```bash
   ansible --version
   ```

3. Se till att EC2-instansen kör och är nåbar via SSH:
   ```bash
   ssh -i ~/.ssh/tofu-key ec2-user@<EC2-IP>
   ```
   Du ska inte göra något på EC2-instansen så gå tillbaka till CloudShell:
   ```bash
   exit
   ```

4. Försök köra Ansible-playbooken:
   ```bash
   ansible-playbook -i inventory apache_setup.yml
   ```
   ❌ **Oj! Något är fel...**  
   Ni måste analysera felen och fixa konfigurationen!

5. Felsök och fixa problemen:
   - Läs felmeddelandena och försök förstå vad som saknas.
   - Titta igenom `apache_setup.yml` och andra filer i repot.
   - Kontrollera vanliga problem:
     - Har Apache installerats korrekt?
     - Är sökvägar riktiga?
     - Startas Apache som en tjänst vid omstart?
   - Använd Ansible-dokumentationen för att hitta rätt syntax och lösningar.

6. Testa om playbooken fungerar efter fixarna:
   ```bash
   ansible-playbook -i inventory apache_setup.yml
   ```
   Om allt fungerar ska du:
   - Kunna besöka webbservern på instansens IP-adress  (med http, inte https) i en webbläsare.
   - Se en korrekt laddad webbsida genom att besöka `http://<EC2-IP>`
   - Bekräfta att Apache startas automatiskt vid omstart:
   ```bash
   aws ec2 reboot-instances --instance-ids <INSTANCE_ID>
   ```
   Där `<INSTANCE_ID>` är ID:t för EC2-instansen. Det kan hittas i AWS Console eller via:
   ```bash
   aws ec2 describe-instances --query "Reservations[*].Instances[*].[Tags[?Key=='Name']|[0].Value, InstanceId]" --output table
   ```

7. Testa hur enkelt det är att göra ändringar och köra om den:
   - Ändra titeln på webbsidan genom att redigera files/index.html.
   - Efter att du gjort en ändring, kör bara:
   ```bash
   ansible-playbook -i inventory apache_setup.yml
   ```
   - Se hur ändringen sjösätta och kontrollera det genom at besöka hemsidan i din browser.   

8. **Viktigt:** Städa upp efter dig genom att ta bort EC2-instansen:
   ```bash
   tofu destroy # måste köras i katalogen där OpenTofu filerna som skapade EC2-instansen finns
   ```
   Eller välj terminate i AWS Console EC2-meny

---

## Hjälpmedel och resurser

### Länkar
- [Ansible Dokumentation](https://docs.ansible.com/ansible/latest/index.html)
- [Service Module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
- [Template Module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html)
- [YAML Syntax](https://yaml.org/spec/1.2/spec.html)
