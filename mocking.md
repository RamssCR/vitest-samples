# Mocking

1. Espías de funciones
```JS
import { expect, test, vi } from 'vitest'

test('espia el mensaje del console.log', () => {
  const spiedLog = vi.spyOn(console, 'log')

  console.log('respuesta espiada')

  expect(spiedLog).toHaveBeenCalledWith('respuesta espiada')
})
```

2. Mockea un módulo
```JS
// suma.js
export const add = (a, b) => a + b

// restaSuma.js
import { add } from './suma.js'

export const restaSuma = (value) => add(10, 20) - value

// suma.spec.js
import { describe, expect, test, vi } from 'vitest'
import { add } from './suma.js'
import { restaSuma } from './restaSuma.js'

vi.mock('./suma.js')

describe('Suite de mockeo', () => {
  test('mockea el resultado del la funcion add', () => {
    add.mockReturnValue(10)

    const result = restaSuma(5)
    expect(result).toEqual(5)
  })
})
```

3. Mockea una dependencia
```JS
// services/zoho.js
import axios from 'axios'

export const getRandomReport = async (reportUrl) => await axios.get(reportUrl)

// services/zoho.spec.js
import { expect, test, vi } from 'vitest'
import axios from 'axios'
import { getRandomReport } from '@services/zoho'

vi.mock('axios')

test('retorna los datos de un reporte correctamente', async () => {
  axios.get.mockResolvedValue({ data: [{ id: 1, name: 'reporte' }] })

  const report = await getRandomReport('https://mock.api.com/endpoint')

  expect(report).toBe({ data: [{ id: 1, name: 'reporte' }] })
  expect(axios.get).toHaveBeenCalledWith('https://mock.api.com/endpoint')
})
```

4. Mockea variables de entorno
```JS
import { expect, test, vi } from 'vitest'

test('incrusta un nombre en una variable de entorno', () => {
  vi.stubEnv('PERSON_NAME', 'Carlos')

  const greeting = `Mi nombre es ${process.env.PERSON_NAME}`
  expect(greeting).toBe('Mi nombre es Carlos')
})
```

5. Mockea una consulta a una tabla de base de datos (Sequelize)
```JS
// services/user.js
export const getAllUsers = async ({ page = 1, limit = 10, offset = 0 } = {}) => {
  const { count, rows } = await User.findAndCountAll({
    limit,
    offset,
    include: [{ model: Role }],
    order: [['createdAt', 'DESC']],
  })

  return {
    items: rows,
    page,
    limit,
    total: count,
    pages: Math.ceil(count / limit),
  }
}

// services/user.spec.js
```JS
import { describe, expect, test, vi } from 'vitest'
import { Role } from '#models/role'
import { User } from '#models/user'
import { getAllUsers } from '#services/user'

describe('User Service - GET Queries', () => {
  test('obtiene todos los usuarios', async () => {
    const mockUsers = [
      { id: 1, email: 'user1@example.com' },
      { id: 2, email: 'user2@example.com' }
    ]
    User.findAndCountAll = vi.fn().mockResolvedValue({ count: mockUsers.length, rows: mockUsers })
    /*
        o incluso se puede hacer asi:
        vi.spyOn(User, 'findAndCountAll').mockResolvedValue({ count: mockUsers.length, rows: mockUsers })
    */

    const result = await getAllUsers()

    expect(User.findAndCountAll).toHaveBeenCalledWith({
      include: [{ model: Role }],
      order: [['createdAt', 'DESC']],
      limit: 10,
      offset: 0
    })
    expect(result).toEqual({
      items: mockUsers,
      page: 1,
      limit: 10,
      pages: 1,
      total: mockUsers.length
    })
  })
})
```
