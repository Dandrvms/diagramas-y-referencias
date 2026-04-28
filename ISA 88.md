````mermaid
classDiagram
    class ProcessCell{}
    class Unit{}
    class EquipmentModule{}
    class ControlModule{}

    class RecipeEntity{}
    class GeneralRecipe{}
    class SiteRecipe{}
    class MasterRecipe{}
    class ControlRecipe{}

    class Procedure{}
    class UnitProcedure{}
    class Operation{}
    class Phase{}

    class EquipmentPhase


    ProcessCell -- Unit
    Unit -- EquipmentModule
    Unit -- ControlModule
    EquipmentModule -- ControlModule
    EquipmentModule -- EquipmentPhase

    RecipeEntity <|-- GeneralRecipe
    RecipeEntity <|-- SiteRecipe
    RecipeEntity <|-- MasterRecipe
    MasterRecipe -- ControlRecipe

    Procedure -- UnitProcedure
    UnitProcedure -- Operation
    Operation -- Phase

    Phase -- EquipmentPhase
    ControlRecipe -- Procedure
    MasterRecipe -- Procedure
```
