# Guía para abrir Pull Request al repositorio original

## Estado del repositorio (2026-05-07)

**Rama activa:** `develop`  
**Fork:** `https://github.com/TICGAL-GLPI-Plugins/glpi-agent.git`  
**Upstream:** `https://github.com/glpi-project/glpi-agent.git`

### Commits listos en develop (por encima de upstream)

| Hash | Mensaje |
|---|---|
| `f38648e4e` | Add DRIVES field to Hyper-V VM inventory to collect VHD disk data |
| `4fed5e4fa` | Add support for collecting virtual hard disk paths and provisioned sizes for Hyper-V VMs |
| `25a5ba823` | feat: enhance Hyper-V integration to collect VHD sizes and update test cases |

### Archivos modificados respecto a upstream

| Archivo | Cambio |
|---|---|
| `lib/GLPI/Agent/Task/Inventory/Virtualization/HyperV.pm` | Nueva query WMI `MSVM_StorageAllocationSettingData` + PowerShell `Get-VHD` para tamaños + campo `DRIVES` en objeto VM |
| `lib/GLPI/Agent/Inventory.pm` | `DRIVES` declarado como campo permitido en `VIRTUALMACHINES` |
| `t/tasks/inventory/virtualization/hyperv.t` | Casos `2008` y `qa` actualizados con `DRIVES` esperados + mock de `runPowerShell` |
| `resources/win32/wmi/2008-MSVM_StorageAllocationSettingData.wmi` | Fixture nuevo para test case `2008` |
| `resources/win32/wmi/qa-MSVM_ComputerSystem.wmi` | Fixture nuevo para test case `qa` |
| `resources/win32/wmi/qa-MSVM_MemorySettingData.wmi` | Fixture nuevo para test case `qa` |
| `resources/win32/wmi/qa-MSVM_ProcessorSettingData.wmi` | Fixture nuevo para test case `qa` |
| `resources/win32/wmi/qa-MSVM_StorageAllocationSettingData.wmi` | Fixture nuevo para test case `qa` |
| `Changes` | Entrada añadida en v1.18 documentando el nuevo soporte de `DRIVES` |

---

## Pasos para abrir la Pull Request

### Paso 1 — Borrar la rama feature (ya no es necesaria)

```bash
git branch -d feature/hyper-v-integration
git push origin --delete feature/hyper-v-integration
```

### Paso 2 — Ejecutar los tests en local

El CI del proyecto ejecuta tests en Linux, Windows y macOS. Verificar al menos en Linux antes de abrir la PR:

```bash
cd glpi-agent
make test
make test TEST_AUTHOR=1
```

`TEST_AUTHOR=1` activa checks adicionales obligatorios: Perl Critic, POD syntax, whitespace.

### Paso 3 — Sincronizar con upstream justo antes de abrir la PR

Por si han llegado commits nuevos al upstream entre medias:

```bash
git fetch upstream
git merge upstream/develop
git push origin develop
```

### Paso 4 — Abrir la Pull Request en GitHub

- **Desde:** `TICGAL-GLPI-Plugins/glpi-agent` rama `develop`
- **Hacia:** `glpi-project/glpi-agent` rama `develop`
- **Título sugerido:** `feat: collect virtual hard disk info for Hyper-V VMs`
- **Descripción:** explicar que se añade el campo `DRIVES` en las VMs Hyper-V con ruta, tamaño en MB y nombre del fichero VHD, sin modificaciones en el servidor GLPI.

---

## Notas importantes

- El proyecto **no tiene CONTRIBUTING.md** pero sigue **Conventional Commits** estrictamente.
- El CI falla si hay warnings de Perl Critic o errores de POD — ejecutar siempre `TEST_AUTHOR=1` antes del push final.
- GLPI core (`glpi_anadat`) **no necesita modificaciones** — `VirtualMachine.php` ya gestiona el campo `drives → Volume → glpi_items_disks`.
- Los discos de red (rutas UNC `\\servidor\ruta`) quedan distinguibles de los locales por el contenido del campo `device` en `glpi_items_disks`.
