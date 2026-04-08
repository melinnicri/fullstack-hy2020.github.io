# Conceptos básicos de React:

# Ejercicio para practicar en React:

- La forma más sencilla de empezar es usar una herramienta llamada Vite.
- Creemos una nueva app usando la herramienta create-vite:
    - se instala npm create vite@latest
Queda con las siguientes características:
"""
PS C:...\fullstackopen2026> npm create vite@latest
Need to install the following packages:
create-vite@9.0.4
Ok to proceed? (y) y

> npx
> create-vite

│
◇  Project name:
│  part1
│
◇  Select a framework:
│  React
│
◇  Select a variant:
│  JavaScript
│
◇  Install with npm and start now?
│  No
│
◇  Scaffolding project in C:...\fullstackopen2026\part1...
│
└  Done. Now run:

  cd part1
  npm install
  npm run dev
"""
Luego se instala npm en la carpeta llamada part1 y queda:

"""
PS C:...\fullstackopen2026> cd part1
PS C:...\fullstackopen2026\part1> npm install

added 151 packages, and audited 152 packages in 15s

36 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
"""
Se abre con:
npm run dev

Y queda:

"""
> part1@0.0.0 dev
> vite


  VITE v8.0.7  ready in 469 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
  """

  Se abre una página web: http://localhost:5173/



  Tarea 1:

  1.6: UniCafe Paso 1
Como muchas empresas, la cafetería estudiantil Unicafe de la Universidad de Helsinki también recoge ahora opiniones de los clientes. Crea una aplicación de comentarios online para Unicafe. Solo hay tres opciones de respuesta: buena, neutral y mala.

La app debería mostrar el número de cada comentario. La app puede mostrar, por ejemplo, De lo siguiente:

localhost:3000/
give feedback
good - neutral - bad

statistics
good 6
neutral 2
bad 1

Ten en cuenta que la app solo necesita ejecutarse durante una sesión del navegador. Por ejemplo. Cuando la página se actualiza, las estadísticas pueden desaparecer.

Se recomienda seguir la misma estructura que en el material y en el ejercicio anterior, es decir, el contenido del archivo main.jsx es el siguiente:

"""
import ReactDOM from 'react-dom/client'
import App from './App'

ReactDOM.createRoot(document.getElementById('root')).render(<App />)
"""

El resto de la aplicación puede hacerse en App.jsx. El contenido del archivo puede ser inicialmente el siguiente:

"""
import { useState } from 'react'

const App = () => {
  // tallenna napit omaan tilaansa
  const [good, setGood] = useState(0)
  const [neutral, setNeutral] = useState(0)
  const [bad, setBad] = useState(0)

  return (
    <div>
      code here
    </div>
  )
}

export default App
"""

# Respuesta 1.6:

"""
App.jsx:
import { useState } from 'react'

const Button = ({ onClick, text }) => (
  <button onClick={onClick}>{text}</button>
)

const StatisticLine = ({ text, value }) => (
  <tr>
    <td>{text}</td>
    <td>{value}</td>
  </tr>
)

const Statistics = ({ good, neutral, bad }) => {
  const total = good + neutral + bad

  if (total === 0) {
    return <p>No feedback given</p>
  }

  return (
    <table>
      <tbody>
        <StatisticLine text="good" value={good} />
        <StatisticLine text="neutral" value={neutral} />
        <StatisticLine text="bad" value={bad} />
      </tbody>
    </table>
  )
}


const App = () => {
  const [good, setGood] = useState(0)
  const [neutral, setNeutral] = useState(0)
  const [bad, setBad] = useState(0)

  return (
    <div>
      <h1>give feedback</h1>
      <Button onClick={() => setGood(good + 1)} text="good" />
      <Button onClick={() => setNeutral(neutral + 1)} text="neutral" />
      <Button onClick={() => setBad(bad + 1)} text="bad" />

      <h1>statistics</h1>
      <Statistics good={good} neutral={neutral} bad={bad} />
    </div>
  )
}

export default App
"""

1.7: Unicafe Paso 2
Amplía la app para que muestre más estadísticas sobre el feedback: la cantidad total, la media (valor bueno 1, neutral 0, malo -1) y el porcentaje de feedback que ha sido positivo:

localhost:3000/
give feedback
good - neutral - bad

statistics
good 6
neutral 2
bad 1
all 9
average 0.5555555555555556
positive 66.66666666666667%

# Respuesta 1.7:

App.jsx
│
└── const App           ← componente principal
        ├── useState
        └── return (
              <button>good</button>
              <button>neutral</button>
              <button>bad</button>
              <p>good {good}</p>
              <p>neutral {neutral}</p>
              <p>bad {bad}</p>
              <p>all {total}</p>
              <p>average {average}</p>
              <p>positive {positive}</p>
            )

"""
App.jsx:
import { useState } from 'react'

const App = () => {
  const [good, setGood] = useState(0)
  const [neutral, setNeutral] = useState(0)
  const [bad, setBad] = useState(0)

  const total = good + neutral + bad
  const average = total === 0 ? 0 : (good - bad) / total
  const positive = total === 0 ? 0 : (good / total) * 100

  return (
    <div>
      <h1>give feedback</h1>
      <button onClick={() => setGood(good + 1)}>good</button>
      <button onClick={() => setNeutral(neutral + 1)}>neutral</button>
      <button onClick={() => setBad(bad + 1)}>bad</button>

      <h1>statistics</h1>
      {total === 0 ? (
        <p>No feedback given</p>
      ) : (
        <div>
          <p>good {good}</p>
          <p>neutral {neutral}</p>
          <p>bad {bad}</p>
          <p>all {total}</p>
          <p>average {average}</p>
          <p>positive {positive} %</p>
        </div>
      )}
    </div>
  )
}

export default App
"""

1.8: Unicafe Paso 3
Refactoriza tu aplicación para que la visualización de estadísticas se separe de la responsabilidad de su propio componente, Estadística. El estado de la app se mantiene en el componente raíz de la App.

Ten en cuenta que los componentes no deben definirse dentro de otro componente:

"""
// oikea paikka komponentin määrittelyyn
const Statistics = (props) => {
  // ...
}

const App = () => {
  const [good, setGood] = useState(0)
  const [neutral, setNeutral] = useState(0)
  const [bad, setBad] = useState(0)

  // EI NÄIN!!! eli älä määrittele komponenttia 
  // toisen komponentin sisällä!
  const Statistics = (props) => {
    // ...
  }

  return (
    // ...
  )
}
"""

# Respuesta 1.8:
App.jsx
│
├── const Button        ← fuera de App
├── const StatisticLine ← fuera de App
├── const Statistics    ← fuera de App
│
└── const App           ← componente principal
        ├── useState
        └── return (
              <Button good />
              <Button neutral />
              <Button bad />
              <Statistics />
            )

"""
App.jsx:
import { useState } from 'react'

// Componente para los botones
const Button = ({ onClick, text }) => (
  <button onClick={onClick}>{text}</button>
)

// Componente para una sola fila de la estadística
const StatisticLine = ({ text, value }) => (
  <tr>
    <td>{text}</td>
    <td>{value}</td>
  </tr>
)

// Componente que agrupa toda la lógica de visualización
const Statistics = ({ good, neutral, bad }) => {
  const total = good + neutral + bad
  
  // Paso 1.9: Renderizado condicional
  if (total === 0) {
    return (
      <div>
        <h1>statistics</h1>
        <p>No feedback given</p>
      </div>
    )
  }

  const average = (good - bad) / total
  const positive = (good / total) * 100

  return (
    <div>
      <h1>statistics</h1>
      {/* Paso 1.11: Uso de tabla para evitar warnings de consola */}
      <table>
        <tbody>
          <StatisticLine text="good" value={good} />
          <StatisticLine text="neutral" value={neutral} />
          <StatisticLine text="bad" value={bad} />
          <StatisticLine text="all" value={total} />
          <StatisticLine text="average" value={average.toFixed(2)} />
          <StatisticLine text="positive" value={`${positive.toFixed(2)} %`} />
        </tbody>
      </table>
    </div>
  )
}

const App = () => {
  // El estado vive en el componente raíz (App)
  const [good, setGood] = useState(0)
  const [neutral, setNeutral] = useState(0)
  const [bad, setBad] = useState(0)

  return (
    <div>
      <h1>give feedback</h1>
      <Button onClick={() => setGood(good + 1)} text="good" />
      <Button onClick={() => setNeutral(neutral + 1)} text="neutral" />
      <Button onClick={() => setBad(bad + 1)} text="bad" />

      {/* Pasamos los estados como props */}
      <Statistics good={good} neutral={neutral} bad={bad} />
    </div>
  )
}

export default App
"""

1.9: Unicafe Step4
Cambiar la aplicación para que las estadísticas numéricas solo se muestren si ya se ha dado retroalimentación:

localhost:3000/
give feedback
good - neutral - bad

statistics
no feedback given

# Respuesta 1.9:
"""
App.jsx:
import { useState } from 'react'

const Button = ({ onClick, text }) => (
  <button onClick={onClick}>{text}</button>
)

const StatisticLine = ({ text, value }) => (
  <tr>
    <td>{text}</td>
    <td>{value}</td>
  </tr>
)

const Statistics = ({ good, neutral, bad }) => {
  const total = good + neutral + bad

  // 1.9: Renderizado condicional
  // Si el total es 0, detenemos la ejecución aquí y devolvemos el mensaje
  if (total === 0) {
    return (
      <div>
        <h1>statistics</h1>
        <p>No feedback given</p>
      </div>
    )
  }

  // Si el total es > 0, el código sigue y renderiza las estadísticas
  const average = (good - bad) / total
  const positive = (good / total) * 100

  return (
    <div>
      <h1>statistics</h1>
      <table>
        <tbody>
          <StatisticLine text="good" value={good} />
          <StatisticLine text="neutral" value={neutral} />
          <StatisticLine text="bad" value={bad} />
          <StatisticLine text="all" value={total} />
          <StatisticLine text="average" value={average} />
          <StatisticLine text="positive" value={positive + " %"} />
        </tbody>
      </table>
    </div>
  )
}

const App = () => {
  const [good, setGood] = useState(0)
  const [neutral, setNeutral] = useState(0)
  const [bad, setBad] = useState(0)

  return (
    <div>
      <h1>give feedback</h1>
      <Button onClick={() => setGood(good + 1)} text="good" />
      <Button onClick={() => setNeutral(neutral + 1)} text="neutral" />
      <Button onClick={() => setBad(bad + 1)} text="bad" />

      <Statistics good={good} neutral={neutral} bad={bad} />
    </div>
  )
}

export default App
"""


1.10: UniCafe Step5
Procedamos con la refactorización de la aplicación. Diferenciar los siguientes dos componentes

Botón equivalente a un solo botón de retroalimentación
StatisticLine se encarga de los datos de las filas estadísticas, por ejemplo, Sobre mostrar la media
Para refinar, el componente StatisticLine siempre muestra una fila de estadísticas, por lo que la aplicación utiliza el componente varias veces para renderizar todas las filas de estadísticas

"""
const Statistics = (props) => {
  /// ...
  return(
    <div>
      <StatisticLine text="good" value={...} />
      <StatisticLine text="neutral" value={...} />
      <StatisticLine text="bad" value={...} />
      // ...
    </div>
  )
}
"""

El estado de la app sigue manteniéndose en la aplicación de componente raíz.

# Respuesta 1.10:
"""
App.jsx:
import { useState } from 'react'

// Componente para un solo botón (Paso 1.10)
const Button = ({ onClick, text }) => (
  <button onClick={onClick}>
    {text}
  </button>
)

// Componente para una sola línea de estadística (Paso 1.10)
const StatisticLine = ({ text, value }) => (
  <p>{text} {value}</p>
)

const Statistics = ({ good, neutral, bad }) => {
  const total = good + neutral + bad

  // Renderizado condicional (mantenido del paso 1.9)
  if (total === 0) {
    return (
      <div>
        <h1>statistics</h1>
        <p>No feedback given</p>
      </div>
    )
  }

  return (
    <div>
      <h1>statistics</h1>
      {/* Refactorización: Usamos StatisticLine varias veces */}
      <StatisticLine text="good" value={good} />
      <StatisticLine text="neutral" value={neutral} />
      <StatisticLine text="bad" value={bad} />
      <StatisticLine text="all" value={total} />
      <StatisticLine text="average" value={(good - bad) / total} />
      <StatisticLine text="positive" value={(good / total) * 100 + " %"} />
    </div>
  )
}

const App = () => {
  // El estado se mantiene en el componente raíz
  const [good, setGood] = useState(0)
  const [neutral, setNeutral] = useState(0)
  const [bad, setBad] = useState(0)

  return (
    <div>
      <h1>give feedback</h1>
      {/* Refactorización: Usamos el componente Button */}
      <Button onClick={() => setGood(good + 1)} text="good" />
      <Button onClick={() => setNeutral(neutral + 1)} text="neutral" />
      <Button onClick={() => setBad(bad + 1)} text="bad" />

      <Statistics good={good} neutral={neutral} bad={bad} />
    </div>
  )
}

export default App
"""

1.11*: Unicafe Step6
Implementa la visualización de estadísticas como una tabla HTML para que tu aplicación se vea algo así:

localhost:3000/
give feedback
good - neutral - bad

statistics
good 5
neutral 2
bad 1
all 8
average 0.5
positive 62.5%

Asegúrate de mantener la consola abierta en todo momento. Si recibes la siguiente advertencia en tu consola:

Dice en una imagen de consola:
Warning: validateDOMNesting(...): <tr> cannot appear as a child of <table>. Add a <tbody> 
to your code to match the DOM tree generated by the browser.

in tr (at src/index.js:5)
in Statistic (at src/index.js:34)
in table (at src/index.js:28)
in Statistics (at src/index.js:26)
in div (at src/index.js:51)
in Statistics (at src/index.js:46)
in div (at src/index.js:56)
in App (at src/index.js:56)

Toma las medidas necesarias para que la advertencia desaparezca. Si es necesario, busca un mensaje de error en Google.

Ahora y en el futuro, ¡asegúrate de no ver ninguna advertencia en tu consola!

# Respuesta 1.11:
"""
App.jsx:
import { useState } from 'react'

const Button = ({ onClick, text }) => (
  <button onClick={onClick}>{text}</button>
)

// StatisticLine ahora devuelve una fila de tabla (tr) con sus celdas (td)
const StatisticLine = ({ text, value }) => (
  <tr>
    <td>{text}</td>
    <td>{value}</td>
  </tr>
)

const Statistics = ({ good, neutral, bad }) => {
  const total = good + neutral + bad

  if (total === 0) {
    return (
      <div>
        <h1>statistics</h1>
        <p>No feedback given</p>
      </div>
    )
  }

  const average = (good - bad) / total
  const positive = (good / total) * 100

  return (
    <div>
      <h1>statistics</h1>
      {/* IMPORTANTE: Usamos <table> y envolvemos los StatisticLine en <tbody> 
        para evitar el error "validateDOMNesting" en la consola.
      */}
      <table>
        <tbody>
          <StatisticLine text="good" value={good} />
          <StatisticLine text="neutral" value={neutral} />
          <StatisticLine text="bad" value={bad} />
          <StatisticLine text="all" value={total} />
          <StatisticLine text="average" value={average.toFixed(1)} />
          <StatisticLine text="positive" value={positive.toFixed(1) + " %"} />
        </tbody>
      </table>
    </div>
  )
}

const App = () => {
  const [good, setGood] = useState(0)
  const [neutral, setNeutral] = useState(0)
  const [bad, setBad] = useState(0)

  return (
    <div>
      <h1>give feedback</h1>
      <Button onClick={() => setGood(good + 1)} text="good" />
      <Button onClick={() => setNeutral(neutral + 1)} text="neutral" />
      <Button onClick={() => setBad(bad + 1)} text="bad" />

      <Statistics good={good} neutral={neutral} bad={bad} />
    </div>
  )
}

export default App
"""

1.12*: Anécdotas Paso 1
En la producción de software, hay innumerables anécdotas o pequeñas "frases rápidas" que cristalizan las verdades eternas de la industria.

Amplía la siguiente aplicación para que tenga un botón que puedas pulsar y mostrar una anécdota aleatoria relacionada con la ingeniería de software:

"""
import { useState } from 'react'

const App = () => {
  const anecdotes = [
    'If it hurts, do it more often.',
    'Adding manpower to a late software project makes it later!',
    'The first 90 percent of the code accounts for the first 90 percent of the development time...The remaining 10 percent of the code accounts for the other 90 percent of the development time.',
    'Any fool can write code that a computer can understand. Good programmers write code that humans can understand.',
    'Premature optimization is the root of all evil.',
    'Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it.',
    'Programming without an extremely heavy use of console.log is same as if a doctor would refuse to use x-rays or blood tests when dianosing patients.',
    'The only way to go fast, is to go well.'
  ]
   
  const [selected, setSelected] = useState(0)

  return (
    <div>
      {anecdotes[selected]}
    </div>
  )
}

export default App
"""


El contenido del archivo main.jsx es el mismo que en las tareas anteriores.

Google te explica cómo usar JavaScript para generar números aleatorios apropiados. Recuerda que puedes probar, por ejemplo, generando números aleatorios en la consola.

La app puede mostrar, por ejemplo, De lo siguiente:

localhost:3000/
If it hurts, do it more often
botón: next anecdote

# Respuesta 1.12:
"""
App.jsx:
import { useState } from 'react'

const App = () => {
  const anecdotes = [
    'If it hurts, do it more often.',
    'Adding manpower to a late software project makes it later!',
    'The first 90 percent of the code accounts for the first 90 percent of the development time...The remaining 10 percent of the code accounts for the other 90 percent of the development time.',
    'Any fool can write code that a computer can understand. Good programmers write code that humans can understand.',
    'Premature optimization is the root of all evil.',
    'Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it.',
    'Programming without an extremely heavy use of console.log is same as if a doctor would refuse to use x-rays or blood tests when dianosing patients.',
    'The only way to go fast, is to go well.'
  ]
   
  const [selected, setSelected] = useState(0)

  // Función para manejar el cambio de anécdota
  const handleNextAnecdote = () => {
    // Generamos un índice aleatorio entre 0 y el largo del array - 1
    const randomIndex = Math.floor(Math.random() * anecdotes.length)
    setSelected(randomIndex)
  }

  return (
    <div>
      <p>{anecdotes[selected]}</p>
      <button onClick={handleNextAnecdote}>
        next anecdote
      </button>
    </div>
  )
}

export default App
"""

1.13*: Anécdotas Paso 2
Amplía la aplicación para que sea posible votar sobre la anécdota que se muestra:

localhost:3000/
Premature optimization is the root of all evil.
has 7 votes
botón: vote    next anecdote

Nota: los sonidos de cada anécdota probablemente deberían almacenarse en los campos o en la tabla del objeto en el estado del componente. Recuerda que para actualizar un estado correctamente, necesitas copiar un objeto o una tabla.

Un objeto puede ser copiado, por ejemplo, de los siguientes modos

"""
const votes = { 0: 1, 1: 3, 2: 4, 3: 2 }

const copy = { ...votes }
// kasvatetaan olion kentän 2 arvoa yhdellä
copy[2] += 1     
"""

y una mesa, por ejemplo. De la siguiente manera:

"""
const votes = [1, 4, 6, 3]

const copy = [...votes]
// kasvatetaan taulukon paikan 2 arvoa yhdellä
copy[2] += 1     
"""

Una solución más sencilla probablemente sea usar una tabla. Buscando en Google, puedes encontrar muchos consejos sobre cómo crear una tabla de la longitud deseada que esté llena de ceros, por ejemplo, esto.

# Respuesta 1.13:
"""
App.jsx:
import { useState } from 'react'

const App = () => {
  const anecdotes = [
    'If it hurts, do it more often.',
    'Adding manpower to a late software project makes it later!',
    'The first 90 percent of the code accounts for the first 90 percent of the development time...The remaining 10 percent of the code accounts for the other 90 percent of the development time.',
    'Any fool can write code that a computer can understand. Good programmers write code that humans can understand.',
    'Premature optimization is the root of all evil.',
    'Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it.',
    'Programming without an extremely heavy use of console.log is same as if a doctor would refuse to use x-rays or blood tests when dianosing patients.',
    'The only way to go fast, is to go well.'
  ]

  const [selected, setSelected] = useState(0)
  
  // Creamos un estado para los votos: un array del mismo largo que las anécdotas, lleno de 0
  const [votes, setVotes] = useState(new Array(anecdotes.length).fill(0))

const handleNext = () => {
  let randomIndex
  
  // "Haz esto mientras el número sea igual al que ya tengo"
  do {
    randomIndex = Math.floor(Math.random() * anecdotes.length)
  } while (randomIndex === selected)
  
  setSelected(randomIndex)
}

  const handleVote = () => {
    // 1. Creamos una copia del array de votos actual
    const copy = [...votes]
    // 2. Incrementamos el valor en la posición de la anécdota seleccionada
    copy[selected] += 1
    // 3. Actualizamos el estado con la copia modificada
    setVotes(copy)
  }

  return (
    <div>
      <p>{anecdotes[selected]}</p>
      <p>has {votes[selected]} votes</p>
      <button onClick={handleVote}>vote</button>
      <button onClick={handleNext}>next anecdote</button>
    </div>
  )
}

export default App
"""

1.14*: Anécdotas Paso 3
Y luego está la versión final, que muestra la anécdota que recibió más votos:

Anecdote of the day
Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it.
has 0 votes
botones:  vote    next anecdote

Anecdote with most votes
Any fool can write code that a computer can understand. Good programmers write code that humans can understand.
has 7 votes

Si hay varias anécdotas con el mayor número de votos, basta con mostrar una de ellas.

Esta era la última tarea de la pieza, y es hora de enviar el código a GitHub y marcar las tareas completadas en la aplicación de recuperación.

# Respuesta 1.14:
"""
App.jsx:
import { useState } from 'react'

const App = () => {
  const anecdotes = [
    'If it hurts, do it more often.',
    'Adding manpower to a late software project makes it later!',
    'The first 90 percent of the code accounts for the first 90 percent of the development time...The remaining 10 percent of the code accounts for the other 90 percent of the development time.',
    'Any fool can write code that a computer can understand. Good programmers write code that humans can understand.',
    'Premature optimization is the root of all evil.',
    'Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it.',
    'Programming without an extremely heavy use of console.log is same as if a doctor would refuse to use x-rays or blood tests when dianosing patients.',
    'The only way to go fast, is to go well.'
  ]

  const [selected, setSelected] = useState(0)
  const [votes, setVotes] = useState(new Array(anecdotes.length).fill(0))

  const handleNext = () => {
    let randomIndex
    do {
      randomIndex = Math.floor(Math.random() * anecdotes.length)
    } while (randomIndex === selected)
    setSelected(randomIndex)
  }

  const handleVote = () => {
    const copy = [...votes]
    copy[selected] += 1
    setVotes(copy)
  }

  // Lógica para encontrar la anécdota con más votos
  const maxVotes = Math.max(...votes)
  const mostVotedIndex = votes.indexOf(maxVotes)

  return (
    <div>
      <h1>Anecdote of the day</h1>
      <p>{anecdotes[selected]}</p>
      <p>has {votes[selected]} votes</p>
      <button onClick={handleVote}>vote</button>
      <button onClick={handleNext}>next anecdote</button>

      <h1>Anecdote with most votes</h1>
      {/* Si no hay votos todavía, podemos mostrar un mensaje o simplemente la primera */}
      {maxVotes === 0 ? (
        <p>No votes yet</p>
      ) : (
        <div>
          <p>{anecdotes[mostVotedIndex]}</p>
          <p>has {maxVotes} votes</p>
        </div>
      )}
    </div>
  )
}

export default App
"""