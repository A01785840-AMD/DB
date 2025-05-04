## Justificación del Esquema Entidad-Relación – Pokémon TCG Live

### Propósito del esquema

Este esquema ER representa la estructura de datos del juego *Pokémon Trading Card Game Live*, con el objetivo de modelar los elementos principales del sistema (jugadores, cartas, decks, sets, etc.), sus atributos clave y las relaciones necesarias para garantizar integridad y funcionalidad dentro de una base de datos relacional.

### 1. Entidad: `UserAccount`

* **Atributos:** `ID`, `Name`, `Email`, `Password`, `XP`, `Level`, `Coins`, `Crystal`, `CreationDate`
* **Justificación:** Representa a cada jugador en el sistema. Se almacenan datos personales y del progreso del usuario.
* **Relaciones:**

  * `Owns` con `Card`: permite saber qué cartas posee el usuario.
  * `Builds` con `Deck`: indica qué decks ha creado el jugador.
  * `PlayVs` con `UserAccount`: modela los enfrentamientos entre usuarios.

### 2. Entidad: `Card` (abstracta, especialización)

* **Atributos comunes:** `ID`, `Name`, `Text`, `Format`, `Type`, `Rarity`, `ImgURL`, `IllustratorName`, `EnergyCost`, `EnergyType`
* **Justificación:** Modelo base para las cartas. Se especializa en tres tipos:

  * `PokemonCard`
  * `TrainerItemCard`
  * `EnergyCard`

### 3. Entidad: `PokemonCard`

* **Atributos adicionales:** `HP`, `EvolveFrom`, `Height`, `Weight`, `Category`, `Format`, `Type`, `Weakness`, `Resistance`, ...

* `Weakness`: representa una debilidad frente a un tipo específico de energía (`EnergyType`) y se aplica un multiplicador que incrementa el daño recibido.

* `Resistance`: representa una resistencia frente a un tipo específico de energía (`EnergyType`) y se aplica una constante que reduce el daño recibido.

* **Relaciones:**

  * `CanAttack`: conecta con ataques posibles (`Attack`, `Cost`, `EnergyType`)
  * `Weakness`, `Resistance`: modelan tipos y efectos frente a otras cartas
  * `Appears` con `Deck`: indica en qué decks aparece
  * `IsIn` con `Set`: define a qué subconjunto pertenece

### 4. Entidad: `TrainerItemCard`

* **Atributos adicionales:** `Type`

### 5. Entidad: `EnergyCard`

* **Atributos adicionales:** `Type`

### 6. Entidad: `Deck`

* **Atributos:** `ID`, `Name`, `CreationDate`
* **Relaciones:**

  * `Builds`: asociada a `UserAccount`
  * `Appears`: contiene muchas `Card`

### 7. Entidad: `Set`

* **ID:** Conformada por `SetID` y `SubSetID`
* **Justificación:** Organización de las cartas por series y expansiones oficiales.
* **Relación:** `IsIn` con `Card`

### 8. Relaciones especiales

* `PlayVs`: autorrrelación entre usuarios para registrar enfrentamientos.
* `Owns`: muchos a muchos entre `UserAccount` y `Card`, representando propiedad.
* `CanAttack`: relación entre `PokemonCard` y ataques posibles.
* `Weakness` y `Resistance`: relaciones ternarias que incluyen tipo de energía y multiplicadores / constantes.

## Requerimientos

### **Requerimientos funcionales**

1. **Registro y autenticación de usuarios**

   * Almacenar cuentas con nombre, email, contraseña (encriptada), progreso (`XP`, `Level`), y recursos (`Coins`, `Crystal`).

2. **Gestión de cartas**

   * Soporte para distintos tipos de cartas: Pokémon, Entrenador y Energía.
   * Cada carta debe tener atributos comunes (`Name`, `Text`, `Format`, etc.) y específicos según su tipo.
   * Las cartas pueden pertenecer a un *Set* y a un *SubSet*.

3. **Construcción de decks**

   * Un usuario puede crear varios *decks* y cada uno puede contener múltiples cartas.
   * Las cartas pueden estar en varios decks.

4. **Propiedad de cartas**

   * Un usuario puede poseer múltiples copias de cartas.
   * Se necesita una tabla para representar esta relación con cantidad (opcional).

5. **Sistema de combate**

   * Registrar combates entre usuarios (`PlayVs`).
   * Asociar ataques (`CanAttack`) a cartas Pokémon, con su coste (`EnergyType`) y daño (`Attack`).

6. **Mecánicas del juego**

   * Implementar debilidades y resistencias por tipo de energía:

     * *Debilidad*: se multiplica el daño si se recibe un ataque de cierto tipo.
     * *Resistencia*: se reduce el daño si se recibe un ataque de cierto tipo.

### **Requerimientos de integridad y consistencia**

1. **Integridad referencial**

   * Todas las relaciones deben mantener claves foráneas válidas (por ejemplo, `DeckID` debe referenciar un `Deck` existente).

2. **Evitar duplicidad**

   * Cartas y usuarios deben tener IDs únicos.
   * Una carta no puede aparecer dos veces en el mismo deck sin control.

3. **Especialización de entidad `Card`**

   * La base debe poder distinguir entre `PokemonCard`, `TrainerItemCard` y `EnergyCard`.

4. **Historial y trazabilidad**

   * Fecha de creación en entidades (`UserAccount`, `Deck`, etc.).
   * Posibilidad futura de loguear combates, compras, transacciones u otras acciones.

## Conclusión

Este modelo captura las estructuras fundamentales de *Pokémon TCG Live*, alineándose con los requisitos del juego. Las entidades están bien definidas, las relaciones reflejan dinámicas reales del sistema (construcción de decks, colección de cartas, partidas entre usuarios), y las restricciones permiten evitar inconsistencias y redundancias.

