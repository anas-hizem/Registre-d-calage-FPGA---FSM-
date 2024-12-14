
# Compte Rendu : Registre à Décalage

## 1. Description du Projet
Ce projet consiste à concevoir et simuler un **registre à décalage** en utilisant le langage VHDL.  
Le registre à décalage prend des bits en entrée (`input`) et les décale successivement à chaque front montant de l'horloge (`clk`).  
Le registre est réinitialisé via un signal de reset (`rst`).

### Fichiers Inclus :
- **`bas_d.vhd`** : Code pour un registre D simple.
- **`reg_dec.vhd`** : Code pour un registre à décalage utilisant des registres D (paramétrable par une largeur `N`).
- **`tb_reg_dec.vhd`** : Banc de test pour simuler le registre à décalage.
- **`my_component.vhd`** : Package contenant les composants utilisés.

---

## 2. Objectif
- Simuler et observer le comportement d'un registre à décalage sur **ModelSim**.
- Vérifier que le contenu du registre évolue correctement en fonction des signaux d'entrée (`input`, `clk`, `rst`).

---

## 3. Structure et Fonctionnalités

### a. **Composant `bas_d` (Registre D)**
- Entrées :
  - `clk` : Signal d'horloge.
  - `rst` : Réinitialisation synchrone.
  - `d` : Donnée d'entrée.
- Sortie :
  - `q` : Donnée de sortie.

```vhdl
architecture arch_bas_d of bas_d is
begin
    process (clk, rst)
    begin
        if (rst = '1') then 
            q <= '0';
        elsif (clk'event and clk = '1') then
            q <= d;
        end if;
    end process;
end arch_bas_d;
```

---

### b. **Composant `reg_dec` (Registre à Décalage)**
- Entrées :
  - `clk` : Signal d'horloge.
  - `rst` : Réinitialisation synchrone.
  - `input` : Entrée de décalage.
- Sorties :
  - `output` : Données en sortie du registre (vecteur).

Le composant utilise des instances du `bas_d` pour chaque bit et décale les données d'entrée à chaque front montant de l'horloge.

```vhdl
architecture arch_reg_dec of reg_dec is
    signal tmp : std_logic_vector (N-1 downto 0);
begin
    output <= tmp;
    bd0: bas_d port map (clk, rst, input, tmp(0));
    GEN: for i in 0 to N-2 generate
        bd: bas_d port map (clk, rst, tmp(i), tmp(i+1));
    end generate;
end arch_reg_dec;
```

---

### c. **Banc de Test `tb_reg_dec`**
- Le banc de test génère :
  - Une horloge (`clk`) avec une période de 100 ns.
  - Un signal de reset (`rst`).
  - Une séquence de bits d'entrée (`data`).

```vhdl
architecture arch_tb_reg_dec of tb_reg_dec is
    signal input, rst : std_logic;
    signal clk : std_logic := '0';
    signal output : std_logic_vector (31 downto 0);
    constant period : time := 100 ns;
begin
    clk <= not clk after period / 2;
    rst <= '1', '0' after period;

    process
    begin
        for i in 0 to 256 loop
            input <= data(i);
            wait for period;
        end loop;
    end process;
end arch_tb_reg_dec;
```

---

## 4. Étapes pour la Simulation

### a. **Compilation**
1. Lancez ModelSim.
2. Ajoutez les fichiers VHDL dans le projet :
   - `bas_d.vhd`
   - `reg_dec.vhd`
   - `tb_reg_dec.vhd`
3. Compilez les fichiers.

### b. **Simulation**
1. Chargez le banc de test :
   ```tcl
   vsim tb_reg_dec
   ```
2. Ajoutez tous les signaux dans la fenêtre Wave :
   ```tcl
   add wave *
   ```
3. Lancez la simulation pour une durée suffisante :
   ```tcl
   run 10 us
   ```

---

## 5. Résultats

### Chronogramme:
![pic](https://github.com/user-attachments/assets/169de8fb-1c88-4cc5-a403-9190de9388c3)



Le registre se remplit et décale les bits d'entrée à chaque cycle d'horloge.  
Quand `rst = '1'`, le registre est réinitialisé.

---

## 6. Conclusion
La simulation montre que :
1. Le registre à décalage fonctionne correctement.
2. Les bits sont introduits séquentiellement depuis le signal `input`.
3. Le contenu du registre (`output`) est réinitialisé quand `rst = '1'`.

---

## 7. Outils Utilisés
- **Quartus Lite** : Pour la compilation et le développement.
- **ModelSim** : Pour la simulation.
- **Langage** : VHDL.
