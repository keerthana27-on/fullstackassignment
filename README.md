# fullstackassignment
/* Full-Stack Assignment Solution

Project Structure:

/frontend (React + TypeScript)

/backend (Node.js + Prisma) */


// === BACKEND (Node.js + Prisma) === // /backend/index.ts import express from 'express'; import { PrismaClient } from '@prisma/client'; import cors from 'cors'; import bcrypt from 'bcrypt';

const app = express(); const prisma = new PrismaClient(); app.use(express.json()); app.use(cors());

// Register User app.post('/register', async (req, res) => { const { email, password } = req.body; const hashedPassword = await bcrypt.hash(password, 10); try { const user = await prisma.user.create({ data: { email, password: hashedPassword }, }); res.status(201).json(user); } catch (error) { res.status(400).json({ error: 'User already exists' }); } });

// Login User app.post('/login', async (req, res) => { const { email, password } = req.body; const user = await prisma.user.findUnique({ where: { email } }); if (user && (await bcrypt.compare(password, user.password))) { res.json({ message: 'Login successful' }); } else { res.status(401).json({ error: 'Invalid credentials' }); } });

app.listen(5000, () => console.log('Backend running on port 5000'));

// === PRISMA SCHEMA === // /backend/prisma/schema.prisma // model User { //   id       String @id @default(uuid()) //   email    String @unique //   password String // }

// === FRONTEND (React + TypeScript) === // /frontend/src/App.tsx import React from 'react'; import { BrowserRouter as Router, Route, Routes } from 'react-router-dom'; import Login from './components/Login'; import Register from './components/Register';

const App: React.FC = () => ( <Router> <Routes> <Route path='/login' element={<Login />} /> <Route path='/register' element={<Register />} /> </Routes> </Router> );

export default App;

// /frontend/src/components/Login.tsx import React, { useState } from 'react'; import axios from 'axios';

const Login: React.FC = () => { const [email, setEmail] = useState(''); const [password, setPassword] = useState(''); const handleLogin = async () => { try { await axios.post('http://localhost:5000/login', { email, password }); alert('Login successful'); } catch (error) { alert('Login failed'); } }; return ( <div> <h2>Login</h2> <input type='email' onChange={(e) => setEmail(e.target.value)} placeholder='Email' /> <input type='password' onChange={(e) => setPassword(e.target.value)} placeholder='Password' /> <button onClick={handleLogin}>Login</button> </div> ); }; export default Login;

// /frontend/src/components/Register.tsx import React, { useState } from 'react'; import axios from 'axios';

const Register: React.FC = () => { const [email, setEmail] = useState(''); const [password, setPassword] = useState(''); const handleRegister = async () => { try { await axios.post('http://localhost:5000/register', { email, password }); alert('Registration successful'); } catch (error) { alert('Registration failed'); } }; return ( <div> <h2>Register</h2> <input type='email' onChange={(e) => setEmail(e.target.value)} placeholder='Email' /> <input type='password' onChange={(e) => setPassword(e.target.value)} placeholder='Password' /> <button onClick={handleRegister}>Register</button> </div> ); }; export default Register;

