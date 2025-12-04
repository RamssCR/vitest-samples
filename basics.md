# Ejemplos básicos de Vitest

1. Estructura básica de un test
```JS
import { describe, expect, test } from 'vitest'

/**
 * Suma dos parámetros
 * @param {number} a - valor 1.
 * @param {number} b - valor 2.
 * @returns {number} La suma de ambos valores
 */
const add = (a, b) => a + b

describe('Suite de sumas', () => {
  test('suma dos numeros correctamente', () => {
    const addition = add(3, 6)
    expect(addition).toEqual(9) // o también .toBe(9)
  })
})
```

2. Capturar valores específicos
```JS
import { expect, it } from 'vitest'

it('valida que un string contenga un valor especifico', () => {
  const value = "Mi constante con varias palabras"
  expect(value).toContain('constante')
  expect(value).not.toContain('ñ')
})
```

3. Valida promesas
```JS
import { describe, expect, it } from 'vitest'

describe('Promesas', () => {
  it('resuelve la promesa', () => {
    const action = new Promise((resolve) => resolve('promesa resuelta'))
    expect(action).resolves.toBe('promesa resuelta')
  })
})
```

4. Testea modelos de Sequelize (Sin Hooks)
```JS
// models/role.js
import { DataTypes } from "sequelize"
import { sequelize } from "#config/database.config"

export const Role = sequelize.define('Role', {
  id: {
    type: DataTypes.INTEGER,
    primaryKey: true,
    autoIncrement: true,
  },
  name: {
    type: DataTypes.STRING,
    allowNull: false,
  },
}, {
  timestamps: true,
  paranoid: true,
  defaultScope: {
    attributes: { exclude: ['createdAt', 'updatedAt', 'deletedAt'] }
  },
})
```
```JS
// models/role.spec.js
import { describe, expect, test, vi } from 'vitest'
import { Role } from '#models/role'

describe('Role Model', () => {
  test('crea (pero no guarda) una instancia de Role', () => {
    const role = Role.build({ name: 'admin' })
    expect(role).toBeInstanceOf(Role)
    expect(role.name).toBe('admin')
  })

  test('encuentra una lista de roles', async () => {
    Role.findAll = vi.fn().mockResolvedValue([
      Role.build({ name: 'admin' }),
      Role.build({ name: 'user' }),
    ])

    const roles = await Role.findAll()
    expect(roles).toHaveLength(2)
    expect(roles[0].name).toBe('admin')
    expect(roles[1].name).toBe('user')
  })
})
```
