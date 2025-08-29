import React, { useState, useEffect, useCallback, useRef } from 'react';
import { initializeApp } from 'firebase/app';
import { 
    getAuth, 
    onAuthStateChanged, 
    signInWithEmailAndPassword, 
    createUserWithEmailAndPassword, 
    signOut 
} from 'firebase/auth';
import { 
    getFirestore, 
    doc, 
    onSnapshot, 
    setDoc, 
    collection, 
    addDoc, 
    updateDoc, 
    deleteDoc, 
    query, 
    orderBy, 
    getDocs,
    getDoc
} from 'firebase/firestore';

// --- Configuration ---
const firebaseConfig = {
    apiKey: "AIzaSyCnQevJTgS4Hk-sr8EiK3fOLPxmrKT5F90",
    authDomain: "chatbot-2086b.firebaseapp.com",
    projectId: "chatbot-2086b",
    storageBucket: "chatbot-2086b.appspot.com",
    messagingSenderId: "102147147231",
    appId: "1:102147147231:web:3e06c1f6945913c7dbb06a"
};

const API_KEY = "AIzaSyCu3l1SyGm_gIc8i96Kw2RfawVU4DFxB3A";
const GEMINI_API_URL = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${API_KEY}`;
const IMAGEN_API_URL = `https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-002:predict?key=${API_KEY}`;

// --- Initialize Firebase ---
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

// --- Helper Components & Data ---

const GlobalStyles = () => (
    <style>{`
        :root {
            --background-color: #0d0c0f; --surface-color: #1a1a1d; --primary-color: #c724b1;
            --primary-gradient: linear-gradient(to right, #e024a2 0%, #8b34ea 100%);
            --secondary-color: #4ac9ff; --accent-color: #4ac9ff; --text-primary-color: #f0f0f0;
            --text-secondary-color: #a0a0a0; --border-color: #333; --error-color: #ff4757;
            --success-color: #2ed573; --font-family: 'Kanit', sans-serif;
        }
        * { box-sizing: border-box; margin: 0; padding: 0; scrollbar-width: thin; scrollbar-color: var(--primary-color) var(--surface-color); }
        *::-webkit-scrollbar { width: 8px; }
        *::-webkit-scrollbar-track { background: var(--surface-color); }
        *::-webkit-scrollbar-thumb { background-color: var(--primary-color); border-radius: 10px; border: 2px solid var(--surface-color); }
        html, body { font-family: var(--font-family); background-color: var(--background-color); color: var(--text-primary-color); line-height: 1.6; height: 100%; overflow: hidden; }
        .container { display: flex; flex-direction: column; height: 100vh; max-width: 1600px; margin: 0 auto; padding: 1rem; }
        button, input, select, textarea { font-family: var(--font-family); border-radius: 8px; border: 1px solid var(--border-color); background-color: #252528; color: var(--text-primary-color); padding: 0.75rem 1rem; }
        button { cursor: pointer; transition: all 0.2s ease; font-weight: 500; }
        button:hover { opacity: 0.9; transform: translateY(-2px); }
        button:active { transform: scale(0.98) translateY(0); }
        button:disabled { cursor: not-allowed; opacity: 0.5; transform: none; }
        .header { background-color: var(--surface-color); padding: 1rem 1.5rem; border-radius: 12px; margin-bottom: 1rem; border: 1px solid var(--border-color); flex-shrink: 0; }
        .header-top { display: flex; justify-content: space-between; align-items: center; }
        .header-left { display: flex; align-items: center; gap: 1rem; }
        .mobile-menu-btn { display: none; background: transparent; border: none; color: var(--text-secondary-color); font-size: 1.5rem; padding: 0.5rem; }
        .logo { display: flex; align-items: center; gap: 0.75rem; }
        .logo-icon svg { width: 40px; height: 40px; color: var(--primary-color); }
        .logo-text h1 { font-size: 1.5rem; margin: 0; color: var(--text-primary-color); }
        .logo-text p { font-size: 0.9rem; color: var(--text-secondary-color); margin: 0; }
        .auth-section { display: flex; align-items: center; gap: 1rem; }
        .user-profile { display: none; align-items: center; gap: 0.75rem; }
        .user-profile.visible { display: flex; }
        #user-greeting { font-size: 0.9rem; color: var(--text-secondary-color); }
        .logout-btn { background-color: #444; color: var(--text-secondary-color); padding: 0.5rem 1rem; }
        .logout-btn:hover { background-color: var(--error-color); color: white; }
        .mode-selector-wrapper { overflow-x: auto; white-space: nowrap; -webkit-overflow-scrolling: touch; scrollbar-width: none; margin-top: 1rem; }
        .mode-selector-wrapper::-webkit-scrollbar { display: none; }
        .mode-selector { display: flex; flex-wrap: nowrap; gap: 0.5rem; padding-bottom: 0.5rem; }
        .mode-btn { flex: 0 0 auto; padding: 0.75rem 1rem; font-size: 0.9rem; border: 1px solid var(--border-color); background-color: #252528; display: flex; align-items: center; justify-content: center; gap: 0.5rem; }
        .mode-btn svg { width: 18px; height: 18px; opacity: 0.7; flex-shrink: 0; }
        .mode-btn.active { background: var(--primary-gradient); color: white; font-weight: 600; border: none; }
        .mode-btn.active svg { opacity: 1; color: white; }
        .main-content { display: flex; flex-grow: 1; gap: 1rem; overflow: hidden; position: relative; }
        .side-panel { width: 35%; background-color: var(--surface-color); padding: 1.5rem; border-radius: 12px; overflow-y: auto; border: 1px solid var(--border-color); transition: transform 0.3s ease-in-out; }
        .side-panel h3 { color: var(--primary-color); margin-bottom: 1rem; border-bottom: 2px solid var(--primary-color); padding-bottom: 0.5rem; }
        .tip-card { background-color: #252528; border-radius: 8px; padding: 1rem; margin-bottom: 1rem; border-left: 3px solid var(--accent-color); box-shadow: 0 2px 8px rgba(0,0,0,0.2); }
        .tip-card h4 { margin-bottom: 0.5rem; color: var(--accent-color); }
        .tip-card p { font-size: 0.9rem; color: var(--text-secondary-color); }
        .chat-panel { width: 65%; display: flex; flex-direction: column; background-color: var(--surface-color); border-radius: 12px; overflow: hidden; border: 1px solid var(--border-color); }
        .chat-messages { flex-grow: 1; padding: 1.5rem; overflow-y: auto; }
        .message { display: flex; margin-bottom: 1.5rem; max-width: 90%; }
        .message-avatar { font-size: 1.5rem; margin-right: 1rem; width: 40px; height: 40px; display: flex; align-items: center; justify-content: center; border-radius: 50%; background-color: var(--background-color); flex-shrink: 0; overflow: hidden; }
        .message-avatar img { width: 100%; height: 100%; object-fit: cover; }
        .message-content { background-color: #2a2a2a; padding: 1rem; border-radius: 12px; flex-grow: 1; word-wrap: break-word; position: relative; }
        .message-content h3 { margin-top: 1rem; color: var(--secondary-color); } .message-content strong { color: var(--primary-color); }
        .message.user { margin-left: auto; flex-direction: row-reverse; }
        .message.user .message-avatar { margin-left: 1rem; margin-right: 0; }
        .message.user .message-content { background: #3c2a3f; }
        .message-content pre { background-color: #111; padding: 1rem; border-radius: 8px; overflow-x: auto; white-space: pre-wrap; word-wrap: break-word; margin-top: 0.5rem; margin-bottom: 0.5rem; }
        .message-actions { display: flex; flex-wrap: wrap; gap: 0.5rem; margin-top: 1rem; border-top: 1px solid var(--border-color); padding-top: 0.75rem; }
        .action-btn { font-size: 0.8rem; padding: 0.25rem 0.75rem; background-color: #3a3a3a; }
        .generated-image { max-width: 100%; border-radius: 8px; margin-top: 1rem; border: 1px solid var(--border-color); }
        .image-loading-placeholder { background-color: #2a2a2a; border-radius: 8px; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 2rem; margin-top: 1rem; border: 1px dashed var(--border-color); }
        .image-loading-spinner { border: 4px solid rgba(255, 255, 255, 0.2); border-left-color: var(--secondary-color); border-radius: 50%; width: 40px; height: 40px; animation: spin 1s linear infinite; }
        @keyframes spin { to { transform: rotate(360deg); } }
        .message.loading .message-content { display: flex; align-items: center; gap: 10px; }
        .typing-indicator { display: flex; gap: 4px; }
        .typing-indicator span { width: 8px; height: 8px; background-color: var(--text-secondary-color); border-radius: 50%; animation: typing 1s infinite ease-in-out; }
        .typing-indicator span:nth-child(2) { animation-delay: 0.2s; } .typing-indicator span:nth-child(3) { animation-delay: 0.4s; }
        @keyframes typing { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-5px); } }
        .chat-input-area { padding: 1rem; background-color: var(--background-color); border-top: 1px solid var(--border-color); }
        .input-wrapper { display: flex; gap: 0.5rem; }
        .message-input { flex-grow: 1; resize: none; }
        .send-btn { padding: 0.75rem 1.5rem; background: var(--primary-gradient); background-size: 200% auto; color: white; font-weight: 600; border: none; display: flex; align-items: center; gap: 0.5rem; }
        .send-btn:hover { background-position: right center; }
        .send-btn svg { width: 18px; height: 18px; }
        .status-bar { display: flex; justify-content: space-between; align-items: center; font-size: 0.8rem; padding-top: 0.5rem; color: var(--text-secondary-color); flex-shrink: 0; }
        .status-indicator { display: flex; align-items: center; gap: 0.5rem; }
        .status-dot { width: 10px; height: 10px; border-radius: 50%; background-color: #444; }
        .status-dot.active { background-color: var(--success-color); animation: pulse 2s infinite; }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(46, 213, 115, 0.4); } 70% { box-shadow: 0 0 0 10px rgba(46, 213, 115, 0); } 100% { box-shadow: 0 0 0 0 rgba(46, 213, 115, 0); } }
        .backdrop { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 1000; }
        @media (max-width: 1024px) {
            .container { padding: 0; }
            .header, .status-bar { border-radius: 0; padding-left: 1rem; padding-right: 1rem; }
            .main-content { flex-grow: 1; height: calc(100vh - 165px); }
            .chat-panel { width: 100%; border-radius: 0; }
            .side-panel { position: fixed; left: 0; top: 0; width: 80%; max-width: 320px; height: 100%; z-index: 1001; transform: translateX(-100%); border-radius: 0; box-shadow: 5px 0 15px rgba(0,0,0,0.2); }
            .side-panel.show { transform: translateX(0); }
            .backdrop.show { display: block; }
            .mobile-menu-btn { display: block; }
        }
        @media (max-width: 768px) {
            .logo-text h1 { font-size: 1.2rem; }
            .logo-text p { display: none; }
            #user-greeting { display: none; }
            .mode-btn { font-size: 0.8rem; padding: 0.5rem 0.75rem; }
            .main-content { height: calc(100vh - 155px); }
        }
        .modal { display: none; position: fixed; z-index: 1000; left: 0; top: 0; width: 100%; height: 100%; overflow: auto; background-color: rgba(0,0,0,0.7); align-items: center; justify-content: center; }
        .modal.show { display: flex; }
        .modal-content { background-color: var(--surface-color); margin: auto; padding: 2rem; border: 1px solid var(--border-color); width: 90%; max-width: 800px; border-radius: 12px; position: relative; max-height: 90vh; display: flex; flex-direction: column; }
        #loginModal .modal-content { max-width: 400px; }
        .close-btn { position: absolute; top: 1rem; right: 1rem; color: #aaa; font-size: 28px; font-weight: bold; background: none; border: none; cursor: pointer; }
        .modal-content h2 { margin-bottom: 1.5rem; color: var(--primary-color); text-align: center; }
        #loginModal a { color: var(--secondary-color); cursor: pointer; }
        .form-group { flex-grow: 1; min-width: 200px; margin-bottom: 1rem; }
        .form-group label { display: block; margin-bottom: 0.5rem; color: var(--text-secondary-color); }
        #loader { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); display: flex; justify-content: center; align-items: center; z-index: 9999; color: white; flex-direction: column; gap: 1rem; }
        #gallery-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(150px, 1fr)); gap: 1rem; }
        .gallery-item { aspect-ratio: 1 / 1; border-radius: 8px; overflow: hidden; cursor: pointer; transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out; position: relative; }
        .gallery-item:hover { transform: scale(1.05); box-shadow: 0 0 15px rgba(187, 134, 252, 0.5); }
        .gallery-item img { width: 100%; height: 100%; object-fit: cover; }
        .gallery-item .delete-btn { position: absolute; top: 8px; right: 8px; background: rgba(0,0,0,0.6); border: none; color: white; border-radius: 50%; width: 30px; height: 30px; font-size: 16px; line-height: 30px; text-align: center; opacity: 0; transition: opacity 0.2s ease; }
        .gallery-item:hover .delete-btn { opacity: 1; }
        #image-viewer-modal .modal-content { background: transparent; border: none; width: 90vw; height: 90vh; padding: 0; max-width: 1200px; }
        .image-viewer-container { display: flex; flex-direction: column; width: 100%; height: 100%; }
        .image-viewer-container img { flex-grow: 1; object-fit: contain; width: 100%; height: 80%; border-radius: 8px; }
        .image-viewer-prompt { background: rgba(0,0,0,0.7); color: white; padding: 1rem; border-radius: 8px; margin-top: 1rem; font-size: 0.9rem; max-height: 20%; overflow-y: auto; }
    `}</style>
);

const icons = {
  logo: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M12 3a6 6 0 0 0 9 9 9 9 0 1 1-9-9Z"></path></svg>,
  send: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M17.8 19.2 16 11l3.5-3.5C21 6 21.5 4 21 3c-1-.5-3 0-4.5 1.5L13 8 4.8 6.2c-.5-.1-.9.1-1.1.5l-.3.5c-.2.5-.1 1 .3 1.3L9 12l-2 3H4l-1 1 3 2 2 3 1-1v-3l3-2 3.5 5.3c.3.4.8.5 1.3.3l.5-.2c.4-.3.6-.7.5-1.2z"></path></svg>,
  // Add other icons here for cleanliness
};

const modes = [
    { id: 'general-chat', label: 'คุยทั่วไป', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path></svg> },
    { id: 'promptmaster', label: 'Prompt Master', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><rect x="2" y="2" width="20" height="20" rx="2.18" ry="2.18"></rect><line x1="7" y1="2" x2="7" y2="22"></line><line x1="17" y1="2" x2="17" y2="22"></line><line x1="2" y1="12" x2="22" y2="12"></line><line x1="2" y1="7" x2="7" y2="7"></line><line x1="2" y1="17" x2="7" y2="17"></line><line x1="17" y1="17" x2="22" y2="17"></line><line x1="17" y1="7" x2="22" y2="7"></line></svg> },
    { id: 'novel-writer', label: 'นักเขียนนิยาย', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z"></path><path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z"></path></svg> },
    { id: 'char-chat', label: 'คุยกับตัวละคร', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path><circle cx="9" cy="7" r="4"></circle><path d="M23 21v-2a4 4 0 0 0-3-3.87"></path><path d="M16 3.13a4 4 0 0 1 0 7.75"></path></svg> },
    { id: 'storyboard', label: 'Storyboard', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect><line x1="3" y1="9" x2="21" y2="9"></line><line x1="3" y1="15" x2="21" y2="15"></line><line x1="9" y1="3" x2="9" y2="21"></line><line x1="15" y1="3" x2="15" y2="21"></line></svg> },
    { id: 'image', label: 'Image Prompt', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect><circle cx="8.5" cy="8.5" r="1.5"></circle><polyline points="21 15 16 10 5 21"></polyline></svg> },
    { id: 'gallery', label: 'My Gallery', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><rect x="3" y="3" width="18" height="18" rx="2"></rect><circle cx="8.5" cy="8.5" r="1.5"></circle><path d="M21 15l-5-5L5 21"></path></svg> },
    { id: 'analyzer', label: 'Prompt Analyzer', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><circle cx="11" cy="11" r="8"></circle><line x1="21" y1="21" x2="16.65" y2="16.65"></line><line x1="11" y1="8" x2="11" y2="14"></line><line x1="8" y1="11" x2="14" y2="11"></line></svg> },
    { id: 'character', label: 'สร้างตัวละคร', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M16 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path><circle cx="8.5" cy="7" r="4"></circle><line x1="20" y1="8" x2="20" y2="14"></line><line x1="17" y1="11" x2="23" y2="11"></line></svg> },
    { id: 'library', label: 'คลังตัวละคร', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path></svg> },
    { id: 'favorites', label: 'Prompt โปรด', icon: <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"></polygon></svg> },
];

const systemPrompts = {
    'general-chat': `You are Nova AI, a helpful and creative assistant. Engage in a friendly conversation with the user in Thai. Use emojis and clear line breaks. Always end with an open-ended follow-up question, like "มีอะไรให้ผมช่วยอีกไหมครับ?"`,
    promptmaster: `You are an expert prompt engineer for text-to-video AI. Your task is to take a user's idea and generate a single, detailed, professional, cinematic prompt IN ENGLISH. Incorporate previous context if the user is refining. The prompt must include details about cinematography, lighting, mood, and visual style. After providing the prompt, end your response in Thai with a friendly tone, using emojis and line breaks, and ask for feedback or next steps, like "ชอบ Prompt นี้ไหมครับ? ✨ ต้องการให้ปรับแก้ส่วนไหนเพิ่มเติมบอกได้เลยนะ!".`,
    storyboard: `You are an expert scriptwriter. Take a user's idea and break it down into 3-5 distinct scenes for a short video. For each scene, create a detailed text-to-video prompt IN ENGLISH. The output should be in THAI, clearly structured using Markdown for each scene. After presenting the storyboard, end your response in Thai with a friendly tone and ask a follow-up question, like "นี่คือ Storyboard ที่ร่างไว้ครับ 📜 ชอบโครงเรื่องแบบนี้ไหม หรืออยากจะพัฒนาฉากไหนเป็นพิเศษครับ?".`,
    image: `You are an expert prompt creator for image generation AI. Transform the user's idea into a highly detailed, comma-separated keyword prompt IN ENGLISH. After providing the prompt, end your response in Thai with a friendly tone, using emojis and line breaks, and ask a follow-up question, for example: "นี่คือ Prompt สำหรับสร้างภาพครับ 🎨 ลองนำไปใช้ดูนะครับ หรืออยากให้เพิ่ม/ลดรายละเอียดส่วนไหนไหมครับ?".`,
    analyzer: `You are a world-class prompt engineering analyst. Analyze the user's provided prompt. Provide a structured analysis in THAI with markdown: '**จุดแข็ง:**', '**ข้อเสนอแนะ:**', and '**Prompt ที่ปรับปรุงแล้ว:**'. After the analysis, end your response in Thai with a friendly tone and ask a follow-up question, like "นี่คือผลการวิเคราะห์ครับ 🧐 หวังว่าจะเป็นประโยชน์นะครับ มี Prompt อื่นให้ผมช่วยดูอีกไหมครับ?".`,
    character: `You are a creative writer. Based on the user's input, generate a detailed character profile in THAI, structured with '**ชื่อ/บทบาท:**', '**ลักษณะภายนอก:**', and '**บุคลิก:**'. After presenting the profile, end your response in Thai with a friendly tone, using emojis, and ask for next steps, like "สร้างโปรไฟล์ตัวละครให้แล้วครับ 🧙‍♀️ ต้องการให้เพิ่มรายละเอียดส่วนไหน หรือจะให้ผมนำตัวละครนี้ไปสร้างเรื่องราวต่อเลยดีครับ?".`,
    'novel-writer': `You are a professional novel writer. Write a compelling short story (around 300-500 words) in Thai based on the user's request. Write in an engaging, narrative style. If the user says 'เขียนต่อ' or similar, continue the story logically. Always end your response by asking if they like the direction of the story and what should happen next, e.g., "เรื่องราวดำเนินมาถึงตรงนี้แล้วครับ 📖 คุณคิดว่าเหตุการณ์ต่อไปควรเป็นอย่างไรดีครับ?".`,
};


function App() {
    // --- State Management ---
    const [user, setUser] = useState(null);
    const [userId, setUserId] = useState(null);
    const [isLoadingApp, setIsLoadingApp] = useState(true);
    const [isLoginModalOpen, setLoginModalOpen] = useState(false);
    
    const [currentMode, setCurrentMode] = useState('general-chat');
    const [chatHistory, setChatHistory] = useState({});
    const [currentMessages, setCurrentMessages] = useState([]);
    const [inputValue, setInputValue] = useState('');
    const [isLoadingResponse, setIsLoadingResponse] = useState(false);
    
    const [isSidePanelOpen, setSidePanelOpen] = useState(false);
    const [activeCharacterForChat, setActiveCharacterForChat] = useState(null);

    const [characters, setCharacters] = useState([]);
    const [favorites, setFavorites] = useState([]);
    const [galleryItems, setGalleryItems] = useState([]);

    const [isCharModalOpen, setCharModalOpen] = useState(false);
    const [editingCharacter, setEditingCharacter] = useState(null);
    
    const [isImageViewerOpen, setImageViewerOpen] = useState(false);
    const [viewingImage, setViewingImage] = useState({ src: '', prompt: '' });

    const chatMessagesEndRef = useRef(null);

    // --- API Call Functions ---
    const callApi = useCallback(async (url, payload) => {
        try {
            const response = await fetch(url, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(payload)
            });
            if (!response.ok) throw new Error(`API error: ${response.status} ${response.statusText}`);
            return await response.json();
        } catch (error) { 
            console.error("API call failed:", error); 
            return { error: "ขออภัยครับ เกิดข้อผิดพลาดในการเชื่อมต่อกับ AI" }; 
        }
    }, []);

    // --- Firebase Auth & Data Listeners ---
    useEffect(() => {
        const unsubscribe = onAuthStateChanged(auth, (firebaseUser) => {
            if (firebaseUser) {
                setUser(firebaseUser);
                setUserId(firebaseUser.uid);
                setLoginModalOpen(false);
            } else {
                setUser(null);
                setUserId(null);
                setLoginModalOpen(true);
            }
            setIsLoadingApp(false);
        });
        return () => unsubscribe();
    }, []);

    useEffect(() => {
        if (!userId) {
            setChatHistory({});
            setCharacters([]);
            setFavorites([]);
            setGalleryItems([]);
            return;
        }

        // Chat History Listener
        const userDocRef = doc(db, "users", userId);
        const unsubscribeHistory = onSnapshot(userDocRef, (docSnap) => {
            setChatHistory(docSnap.exists() && docSnap.data().chatHistory ? docSnap.data().chatHistory : {});
        });
        
        // Characters Listener
        const charQuery = query(collection(db, `users/${userId}/characters`), orderBy("createdAt", "desc"));
        const unsubscribeChars = onSnapshot(charQuery, (snapshot) => {
            setCharacters(snapshot.docs.map(d => ({ id: d.id, ...d.data() })));
        });

        // Favorites Listener
        const favQuery = query(collection(db, `users/${userId}/favorites`), orderBy("createdAt", "desc"));
        const unsubscribeFavs = onSnapshot(favQuery, (snapshot) => {
            setFavorites(snapshot.docs.map(d => ({ id: d.id, ...d.data() })));
        });

        // Gallery Listener
        const galleryQuery = query(collection(db, `users/${userId}/gallery`), orderBy("createdAt", "desc"));
        const unsubscribeGallery = onSnapshot(galleryQuery, (snapshot) => {
            setGalleryItems(snapshot.docs.map(d => ({ id: d.id, ...d.data() })));
        });

        return () => {
            unsubscribeHistory();
            unsubscribeChars();
            unsubscribeFavs();
            unsubscribeGallery();
        };
    }, [userId]);

    // --- Chat Logic ---
    const getHistoryKey = useCallback(() => {
        return (currentMode === 'char-chat' && activeCharacterForChat)
            ? `char-chat-${activeCharacterForChat.id}`
            : currentMode;
    }, [currentMode, activeCharacterForChat]);

    const saveHistory = useCallback(async (newHistory) => {
        if (!userId) return;
        const userDocRef = doc(db, "users", userId);
        await setDoc(userDocRef, { chatHistory: newHistory }, { merge: true });
    }, [userId]);

    const addMessageToHistory = useCallback((text, role) => {
        const historyKey = getHistoryKey();
        const newMessage = { role, parts: [{ text }] };
        const currentHistory = chatHistory[historyKey] || [];
        
        let newHistoryForMode = [...currentHistory];
        if (role === 'user' || role === 'assistant') {
            newHistoryForMode.push({ role: role === 'user' ? 'user' : 'model', parts: newMessage.parts });
        }
        
        const updatedChatHistory = { ...chatHistory, [historyKey]: newHistoryForMode };
        setChatHistory(updatedChatHistory);
        saveHistory(updatedChatHistory);

        // This is for local state rendering, separate from persistent history
        setCurrentMessages(prev => [...prev, newMessage]);
    }, [chatHistory, getHistoryKey, saveHistory]);
    
    const handleSendMessage = useCallback(async (userInput) => {
        if (!userInput || isLoadingResponse) return;
        
        setInputValue('');
        addMessageToHistory(userInput, 'user');
        setIsLoadingResponse(true);

        const loadingMessageId = Date.now();
        setCurrentMessages(prev => [...prev, { role: 'loading', id: loadingMessageId }]);
        
        const historyKey = getHistoryKey();
        let historyForAPI = (chatHistory[historyKey] || []).slice(-10);

        let systemInstruction = systemPrompts[currentMode];
        if (currentMode === 'char-chat' && activeCharacterForChat) {
            systemInstruction = `You must act as the character '${activeCharacterForChat.nickname}'. Personality: '${activeCharacterForChat.personality}'. Appearance: '${activeCharacterForChat.appearance}'. Respond in character at all times, in Thai. Use emojis and clear line breaks. Always end with a follow-up question.`;
        }

        const payload = { contents: historyForAPI, systemInstruction: { parts: [{ text: systemInstruction }] } };
        const result = await callApi(GEMINI_API_URL, payload);
        
        setCurrentMessages(prev => prev.filter(m => m.id !== loadingMessageId));

        if (result.error) {
            addMessageToHistory(result.error, 'assistant');
        } else {
            const responseText = result.candidates?.[0]?.content?.parts?.[0]?.text || "ไม่ได้รับคำตอบที่ถูกต้องจาก AI";
            addMessageToHistory(responseText, 'assistant');
        }
        setIsLoadingResponse(false);

    }, [isLoadingResponse, addMessageToHistory, getHistoryKey, chatHistory, currentMode, activeCharacterForChat, callApi]);

    // Effect to update displayed messages when mode changes
    useEffect(() => {
        const historyKey = getHistoryKey();
        const messagesForMode = (chatHistory[historyKey] || []).map(msg => ({
            role: msg.role === 'model' ? 'assistant' : 'user',
            parts: msg.parts
        }));
        
        if (messagesForMode.length === 0) {
             const welcomeMessages = {
                'general-chat': 'สวัสดีครับ! มีอะไรให้ผมช่วยในวันนี้ครับ? ลองถามอะไรก็ได้เลย',
                'promptmaster': 'เริ่มต้นด้วยการบรรยายฉากที่ต้องการ เช่น "ชายคนหนึ่งวิ่งหนีบนตึกระฟ้าตอนฝนตก"',
                'novel-writer': 'กรอกแนวเรื่องและพล็อตเริ่มต้นทางด้านซ้ายเพื่อเริ่มสร้างนิยายของคุณ',
                'char-chat': 'เลือกตัวละครจากด้านซ้ายเพื่อเริ่มการสนทนา',
                'storyboard': 'ใส่ไอเดียหรือเรื่องย่อสั้นๆ แล้วผมจะช่วยแตกเป็นฉากๆ ให้ครับ',
                'image': 'บรรยายภาพที่คุณจินตนาการไว้ เช่น "นักบินอวกาศขี่ม้าบนดาวอังคาร"',
                'analyzer': 'วาง Prompt ที่คุณต้องการให้ผมช่วยวิเคราะห์ลงในช่องแชทได้เลยครับ',
                'character': 'บรรยายลักษณะตัวละครที่คุณอยากสร้าง เช่น "นักรบหญิงในชุดเกราะสีเงิน"',
                'library': 'ที่นี่คือคลังตัวละครของคุณ คุณสามารถเลือก "ใช้งาน" เพื่อนำข้อมูลไปใช้ในโหมดอื่น หรือ "ลบ" ได้',
                'favorites': 'ที่นี่คือคลัง Prompt ที่คุณชื่นชอบ สามารถเลือก "ใช้งาน" เพื่อนำกลับมาใช้ใหม่ได้เลย',
                'gallery': 'ที่นี่คือคลังภาพส่วนตัวของคุณ คลิกที่รูปเพื่อดูภาพขนาดเต็ม'
            };
            let welcomeText = welcomeMessages[currentMode] || `เริ่มต้นใช้งานโหมด '${currentMode}' ได้เลย!`;
            if(currentMode === 'char-chat' && activeCharacterForChat) welcomeText = `เริ่มต้นการสนทนากับ ${activeCharacterForChat.nickname} ได้เลย!`;
            setCurrentMessages([{ role: 'assistant', parts: [{text: welcomeText}] }]);
        } else {
             setCurrentMessages(messagesForMode);
        }
    }, [currentMode, chatHistory, activeCharacterForChat, getHistoryKey]);
    
    // Auto-scroll chat
    useEffect(() => {
        chatMessagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
    }, [currentMessages]);


    // --- UI Handlers ---
    const handleSwitchMode = (newMode) => {
        setCurrentMode(newMode);
        setActiveCharacterForChat(null);
        if (window.innerWidth <= 1024) {
            setSidePanelOpen(false);
        }
    };

    const handleClearHistory = () => {
        if (window.confirm('คุณต้องการล้างประวัติการสนทนาในโหมดนี้ใช่หรือไม่?')) {
            const historyKey = getHistoryKey();
            const updatedHistory = { ...chatHistory, [historyKey]: [] };
            setChatHistory(updatedHistory);
            saveHistory(updatedHistory);
        }
    };

    // --- Component Rendering & Logic ---

    if (isLoadingApp) {
        return (
            <div id="loader">
                <div className="image-loading-spinner"></div>
                <p>กำลังโหลด...</p>
            </div>
        );
    }
    
    const userName = user ? user.email.split('@')[0] : '';
    const capitalizedUserName = userName.charAt(0).toUpperCase() + userName.slice(1);
    
    return (
        <>
            <GlobalStyles />
            {isLoginModalOpen && <LoginModal setLoginModalOpen={setLoginModalOpen} />}

            {user && (
                <div className="container">
                    <Header 
                        userName={capitalizedUserName}
                        currentMode={currentMode}
                        onSwitchMode={handleSwitchMode}
                        onToggleMenu={() => setSidePanelOpen(!isSidePanelOpen)}
                    />
                    <div className="main-content">
                         <div className={`backdrop${isSidePanelOpen ? ' show' : ''}`} onClick={() => setSidePanelOpen(false)}></div>
                         <SidePanel 
                            isOpen={isSidePanelOpen}
                            currentMode={currentMode}
                            characters={characters}
                            favorites={favorites}
                            galleryItems={galleryItems}
                            onOpenCharModal={(char) => { setEditingCharacter(char || null); setCharModalOpen(true); }}
                            onUseCharacter={async (id) => {
                                const docRef = doc(db, `users/${userId}/characters/${id}`);
                                const docSnap = await getDoc(docRef);
                                if (docSnap.exists()) {
                                    const char = docSnap.data();
                                    handleSwitchMode('promptmaster');
                                    setInputValue(`ตัวละคร: ${char.nickname}\nลักษณะ: ${char.appearance}\nนิสัย: ${char.personality}`);
                                }
                            }}
                            onDeleteCharacter={async (id) => {
                                if(window.confirm('คุณต้องการลบตัวละครนี้ใช่หรือไม่?')) {
                                    await deleteDoc(doc(db, `users/${userId}/characters/${id}`));
                                }
                            }}
                             onUseFavorite={async (id) => {
                                const docRef = doc(db, `users/${userId}/favorites/${id}`);
                                const docSnap = await getDoc(docRef);
                                if (docSnap.exists()) {
                                    const fav = docSnap.data();
                                    handleSwitchMode(fav.mode);
                                    setInputValue(fav.prompt);
                                }
                            }}
                            onDeleteFavorite={async (id) => {
                                if(window.confirm('คุณต้องการลบ Prompt นี้ใช่หรือไม่?')) {
                                    await deleteDoc(doc(db, `users/${userId}/favorites/${id}`));
                                }
                            }}
                            onImageViewerOpen={(src, prompt) => {
                                setViewingImage({ src, prompt });
                                setImageViewerOpen(true);
                            }}
                            onDeleteGalleryItem={async (id) => {
                                if(window.confirm('คุณต้องการลบรูปภาพนี้ใช่หรือไม่?')) {
                                    await deleteDoc(doc(db, `users/${userId}/gallery/${id}`));
                                }
                            }}
                            activeCharacterForChat={activeCharacterForChat}
                            onStartCharChat={async (charId) => {
                                const char = characters.find(c => c.id === charId);
                                setActiveCharacterForChat(char);
                            }}
                             onEndCharChat={() => setActiveCharacterForChat(null)}
                             onSendMessage={handleSendMessage}
                             setInputValue={setInputValue}
                         />
                         <ChatPanel
                            messages={currentMessages}
                            inputValue={inputValue}
                            setInputValue={setInputValue}
                            onSendMessage={() => handleSendMessage(inputValue)}
                            isLoading={isLoadingResponse}
                            currentMode={currentMode}
                            activeCharacterForChat={activeCharacterForChat}
                            chatMessagesEndRef={chatMessagesEndRef}
                            callApi={callApi}
                            addMessageToHistory={addMessageToHistory}
                            userId={userId}
                         />
                    </div>
                     <StatusBar isLoading={isLoadingResponse} onClearHistory={handleClearHistory} />
                </div>
            )}
            
            {isCharModalOpen && (
                <CharacterModal 
                    character={editingCharacter} 
                    onClose={() => { setCharModalOpen(false); setEditingCharacter(null); }}
                    userId={userId}
                />
            )}
            {isImageViewerOpen && (
                <ImageViewerModal 
                    src={viewingImage.src} 
                    prompt={viewingImage.prompt} 
                    onClose={() => setImageViewerOpen(false)} 
                />
            )}
        </>
    );
}

// --- Sub-Components ---

const Header = ({ userName, currentMode, onSwitchMode, onToggleMenu }) => {
    return (
        <div className="header">
            <div className="header-top">
                <div className="header-left">
                    <button id="mobile-menu-btn" className="mobile-menu-btn" onClick={onToggleMenu}>☰</button>
                    <div className="logo">
                        <div className="logo-icon">{icons.logo}</div>
                        <div className="logo-text">
                            <h1>Nova AI</h1>
                            <p>ผู้ช่วยสร้างสรรค์อัจฉริยะ</p>
                        </div>
                    </div>
                </div>
                <div className="auth-section">
                    <div className="user-profile visible">
                        <span id="user-greeting">สวัสดี, {userName}</span>
                        <button className="logout-btn" onClick={() => signOut(auth)}>ออกจากระบบ</button>
                    </div>
                </div>
            </div>
            <div className="mode-selector-wrapper">
                <div className="mode-selector">
                    {modes.map(mode => (
                        <button
                            key={mode.id}
                            className={`mode-btn ${currentMode === mode.id ? 'active' : ''}`}
                            onClick={() => onSwitchMode(mode.id)}
                        >
                            {mode.icon}
                            {mode.label}
                        </button>
                    ))}
                </div>
            </div>
        </div>
    );
};

const SidePanel = ({ isOpen, currentMode, characters, onOpenCharModal, onUseCharacter, onDeleteCharacter, favorites, onUseFavorite, onDeleteFavorite, galleryItems, onImageViewerOpen, onDeleteGalleryItem, activeCharacterForChat, onStartCharChat, onEndCharChat, onSendMessage, setInputValue }) => {
    
    const handleAddImageFromUrl = async (userId) => {
        const imageUrl = document.getElementById('galleryImageUrl').value.trim();
        const prompt = document.getElementById('galleryImageDesc').value.trim();
        if (!imageUrl || !userId) return;
        
        await addDoc(collection(db, `users/${userId}/gallery`), {
            imageUrl, prompt, createdAt: new Date()
        });
        document.getElementById('galleryImageUrl').value = '';
        document.getElementById('galleryImageDesc').value = '';
    };
    
    const handleNovelStart = async (userId) => {
        const genre = document.getElementById('novelGenre').value.trim();
        const plot = document.getElementById('novelPlot').value.trim();
        const charId = document.getElementById('novelCharacterSelect').value;
        if (!genre || !plot) {
             alert('กรุณากรอก "แนวเรื่อง" และ "ไอเดีย/พล็อตเรื่อง"');
             return;
        }
        let userInput = `แนวเรื่อง: ${genre}\nพล็อต: ${plot}`;
        if(charId && userId) {
            const char = characters.find(c => c.id === charId);
            if(char) {
                 userInput += `\nตัวละครเอก: ${char.nickname} (ลักษณะ: ${char.appearance}, นิสัย: ${char.personality})`;
            }
        }
        onSendMessage(userInput);
    };

    const renderContent = () => {
        switch(currentMode) {
            case 'general-chat':
                return <><h3>💬 คุยทั่วไป</h3><div className="tip-card"><h4>ผู้ช่วยสร้างสรรค์</h4><p>คุณสามารถพูดคุยกับ AI ได้ทุกเรื่อง, ระดมสมอง, หรือบรีฟงานต่างๆ ได้อย่างอิสระในโหมดนี้</p></div></>;
            case 'promptmaster':
                return <><h3>🎥 Prompt Master</h3><div className="tip-card"><h4>สร้าง Prompt วิดีโอระดับมืออาชีพ</h4><p>แค่บอกไอเดียของคุณ แล้ว Gemini จะช่วยเปลี่ยนให้เป็น Prompt ที่ละเอียดและพร้อมใช้งานทันที คุณสามารถคุยต่อเพื่อปรับแก้ได้</p></div></>;
            case 'storyboard':
                return <><h3>📜 Storyboard Mode</h3><div className="tip-card"><h4>สร้าง Storyboard จากเรื่องย่อ</h4><p>ใส่ไอเดียหรือพล็อตเรื่องสั้นๆ ของคุณ แล้ว Gemini จะช่วยแตกออกมาเป็น Prompt สำหรับแต่ละฉาก (Scene) 3-5 ฉากให้โดยอัตโนมัติ</p></div></>;
            case 'image':
                return <><h3>🖼️ Image Prompt Creator</h3><div className="tip-card"><h4>สร้าง Prompt สำหรับรูปภาพ</h4><p>บรรยายภาพในจินตนาการของคุณ แล้ว Gemini จะสร้าง Prompt ที่ละเอียดเพื่อให้ AI สร้างภาพได้ตรงใจที่สุด</p></div><div className="tip-card"><h4>🚫 Negative Prompt (สิ่งที่ไม่ต้องการ)</h4><div className="form-group"><textarea id="negativePromptInput" className="template-textarea" rows="2" placeholder="เช่น text, watermark, ugly, deformed..."></textarea></div></div></>;
            case 'analyzer':
                return <><h3>🧐 Prompt Analyzer</h3><div className="tip-card"><h4>วิเคราะห์และปรับปรุง Prompt</h4><p>วาง Prompt ที่คุณมีอยู่ แล้ว Gemini จะช่วยวิเคราะห์จุดแข็ง จุดอ่อน และให้คำแนะนำเพื่อปรับปรุงให้ดีขึ้น</p></div></>;
            case 'character':
                return <><h3>🧙 สร้างตัวละคร</h3><div className="tip-card"><h4>ตอนนี้คุณสามารถพิมพ์เพื่อสร้างตัวละครในช่องแชทได้โดยตรง</h4><p>หรือใช้ปุ่มด้านล่างเพื่อเปิดฟอร์มแบบละเอียด</p></div><button onClick={() => onOpenCharModal()} style={{width:'100%', padding: '0.75rem', marginTop: '1rem', background: 'var(--primary-gradient)', color:'white', border:'none'}}>+ เปิดฟอร์มสร้างตัวละคร</button></>;
            case 'library':
                return <><h3>🗂️ คลังตัวละคร</h3><p style={{color:'var(--text-secondary-color)', fontSize: '0.9rem', marginBottom: '1rem'}}>ตัวละครของคุณจะถูกซิงค์ข้ามอุปกรณ์</p><div>{characters.length > 0 ? characters.map(char => (
                    <div key={char.id} className="tip-card">
                        <div style={{display:'flex', alignItems:'center', gap:'1rem'}}>
                           <img src={char.imageUrl || 'https://placehold.co/100x100/2a2a2a/a0a0a0?text=🎭'} style={{width:'50px', height:'50px', borderRadius:'50%', objectFit:'cover'}} alt={char.nickname} />
                           <div style={{flexGrow:1}}><h4>{char.nickname}</h4><p>{char.appearance.substring(0,80)}...</p></div>
                        </div>
                        <div style={{marginTop: '1rem', display:'flex', gap: '0.5rem'}}>
                           <button onClick={() => onUseCharacter(char.id)}>ใช้งาน</button>
                           <button onClick={() => onOpenCharModal(char)}>แก้ไข</button>
                           <button onClick={() => onDeleteCharacter(char.id)} style={{backgroundColor: 'var(--error-color)'}}>ลบ</button>
                        </div>
                    </div>)) : <div className="tip-card"><p>ยังไม่มีตัวละครที่บันทึกไว้</p></div>}</div></>;
            case 'favorites':
                 return <><h3>⭐ Prompt โปรด</h3><p style={{color:'var(--text-secondary-color)', fontSize: '0.9rem', marginBottom: '1rem'}}>Prompt ที่คุณบันทึกไว้จะถูกซิงค์ข้ามอุปกรณ์</p><div>{favorites.length > 0 ? favorites.map(fav => (
                     <div key={fav.id} className="tip-card">
                         <h4>[{fav.mode}]</h4><p>{fav.prompt.substring(0,150)}...</p>
                         <div style={{marginTop: '1rem', display:'flex', gap: '0.5rem'}}>
                            <button onClick={() => onUseFavorite(fav.id)}>ใช้งาน</button>
                            <button onClick={() => onDeleteFavorite(fav.id)} style={{backgroundColor: 'var(--error-color)'}}>ลบ</button>
                         </div>
                     </div>)) : <div className="tip-card"><p>ยังไม่มี Prompt ที่บันทึกไว้</p></div>}</div></>;
            case 'gallery':
                return <><h3>🎨 My Gallery</h3>
                    <div className="tip-card"><h4>เพิ่มรูปภาพใหม่จากลิงก์</h4>
                        <div className="form-group"><input type="url" id="galleryImageUrl" className="template-input" placeholder="วาง URL ของรูปภาพ"/></div>
                        <div className="form-group"><textarea id="galleryImageDesc" className="template-textarea" rows="2" placeholder="คำอธิบายสั้นๆ (ถ้ามี)"></textarea></div>
                        <button onClick={() => handleAddImageFromUrl(auth.currentUser?.uid)} style={{width:'100%', background: 'var(--primary-gradient)', color: 'white', border: 'none'}}>+ เพิ่มไปยังแกลเลอรี</button>
                    </div><p style={{color:'var(--text-secondary-color)', fontSize: '0.9rem', marginBottom: '1rem'}}>ผลงานรูปภาพที่คุณบันทึกไว้</p>
                    <div id="gallery-grid">{galleryItems.length > 0 ? galleryItems.map(item => (
                        <div key={item.id} className="gallery-item">
                            <img src={item.imageUrl} alt="Generated" onClick={() => onImageViewerOpen(item.imageUrl, item.prompt)} />
                            <button className="delete-btn" onClick={() => onDeleteGalleryItem(item.id)}>×</button>
                        </div>)) : <div className="tip-card"><p>ยังไม่มีรูปภาพที่บันทึกไว้</p></div>}</div></>;
            case 'char-chat':
                if (activeCharacterForChat) {
                    return <><h3>💬 คุยกับตัวละคร</h3><div className="tip-card">
                        <h4>กำลังคุยกับ:</h4>
                        <div style={{display:'flex', alignItems:'center', gap:'1rem', marginBottom:'1rem'}}>
                           <img src={activeCharacterForChat.imageUrl || 'https://placehold.co/100x100/1e1e1e/a0a0a0?text=🎭'} style={{width:'50px', height:'50px', borderRadius:'50%', objectFit:'cover'}} alt={activeCharacterForChat.nickname}/>
                           <p style={{color: 'var(--secondary-color)', fontSize: '1.2rem', fontWeight: 'bold'}}>{activeCharacterForChat.nickname}</p>
                        </div>
                        <button onClick={onEndCharChat} style={{width:'100%', padding: '0.5rem', marginTop: '1rem', backgroundColor: 'var(--error-color)'}}>จบการสนทนา</button>
                    </div></>;
                }
                return <><h3>💬 คุยกับตัวละคร</h3>{characters.length > 0 ? characters.map(char => (
                     <div key={char.id} className="tip-card" style={{display:'flex', alignItems:'center', gap:'1rem'}}>
                         <img src={char.imageUrl || 'https://placehold.co/100x100/2a2a2a/a0a0a0?text=🎭'} style={{width:'50px', height:'50px', borderRadius:'50%', objectFit:'cover'}} alt={char.nickname} />
                         <div style={{flexGrow:1}}><h5>{char.nickname}</h5><button onClick={() => onStartCharChat(char.id)} style={{width:'100%'}}>เริ่มคุย</button></div>
                     </div>)) : <div className="tip-card"><p>ยังไม่มีตัวละครที่บันทึกไว้ ไปที่โหมด "สร้างตัวละคร" เพื่อสร้างตัวละครของคุณก่อน</p></div>}</>;
            case 'novel-writer':
                return <><h3>📖 นักเขียนนิยาย</h3><div className="tip-card">
                    <h4>ตั้งค่าเรื่องราวของคุณ</h4>
                    <div className="form-group"><label>แนวเรื่อง (Genre)</label><input type="text" id="novelGenre" className="template-input" list="genres" placeholder="เช่น แฟนตาซี, สืบสวน, รักโรแมนติก"/>
                        <datalist id="genres"><option value="แฟนตาซี"/><option value="ไซไฟ"/><option value="สืบสวนสอบสวน"/><option value="รักโรแมนติก"/><option value="สยองขวัญ"/></datalist>
                    </div>
                    <div className="form-group"><label>เลือกตัวละครเอก (ถ้ามี)</label><select id="novelCharacterSelect" className="template-select"><option value="">-- ไม่เลือก --</option>{characters.map(c => <option key={c.id} value={c.id}>{c.nickname}</option>)}</select></div>
                    <div className="form-group"><label>ไอเดีย/พล็อตเรื่องเริ่มต้น</label><textarea id="novelPlot" className="template-textarea" rows="4" placeholder="เช่น ชายหนุ่มคนหนึ่งตื่นขึ้นมาในโลกที่ไม่คุ้นเคย..."></textarea></div>
                    <button onClick={() => handleNovelStart(auth.currentUser?.uid)} style={{width:'100%', padding: '0.75rem', marginTop: '1rem', backgroundColor: 'var(--secondary-color)', color:'black', fontWeight: 600}}>เริ่มเขียนนิยาย ✨</button>
                </div></>;
            default:
                return <h3>Select a mode</h3>;
        }
    };

    return (
        <div className={`side-panel${isOpen ? ' show' : ''}`}>
            {renderContent()}
        </div>
    );
};

const ChatPanel = ({ messages, inputValue, setInputValue, onSendMessage, isLoading, currentMode, activeCharacterForChat, chatMessagesEndRef, callApi, addMessageToHistory, userId }) => {
    
    const placeholders = {
        'general-chat': 'คุยกับ AI ได้ทุกเรื่องที่นี่...', 'promptmaster': "สร้างฉากไล่ล่าในเมืองตอนกลางคืน...",
        'storyboard': "เรื่องย่อ: นักบินอวกาศค้นพบดาวเคราะห์ดวงใหม่...", 'image': "แมวใส่แว่นกันแดดบนชายหาด...",
        'analyzer': "วาง Prompt ที่ต้องการวิเคราะห์ที่นี่...", 'character': "บรรยายลักษณะตัวละครที่คุณอยากสร้าง...",
        'char-chat': 'เลือกตัวละครจากด้านซ้ายเพื่อเริ่มคุย...', 'novel-writer': 'ใช้เทมเพลตด้านซ้ายเพื่อเริ่มเรื่องราว...',
        'library': "เลือกตัวละครเพื่อใช้งาน", 'favorites': "เลือก Prompt ที่ชื่นชอบเพื่อใช้งาน", 'gallery': "คลังภาพส่วนตัวของคุณ"
    };
    
    const isInputDisabled = isLoading || ['library', 'favorites', 'gallery'].includes(currentMode) || (currentMode === 'char-chat' && !activeCharacterForChat);

    const handleSuggestIdea = async () => {
        if(isLoading || isInputDisabled) return;
        const systemInstruction = "You are a creative idea generator. Provide a single, concise, and inspiring idea in Thai based on the user's selected mode. Do not add any extra text or explanation.";
        const result = await callApi(GEMINI_API_URL, { contents: [{ role: 'user', parts: [{ text: "Suggest an idea" }] }], systemInstruction: { parts: [{ text: systemInstruction }] } });
        const idea = result.candidates?.[0]?.content?.parts?.[0]?.text;
        if (idea && !idea.includes("ขออภัย")) {
            setInputValue(idea);
        }
    }
    
    return (
        <div className="chat-panel">
            <div className="chat-messages">
                {messages.map((msg, index) => (
                    <Message 
                        key={index} 
                        message={msg} 
                        activeCharacterForChat={activeCharacterForChat} 
                        currentMode={currentMode}
                        callApi={callApi}
                        addMessageToHistory={addMessageToHistory}
                        userId={userId}
                     />
                ))}
                <div ref={chatMessagesEndRef} />
            </div>
            <div className="chat-input-area">
                <div className="input-wrapper">
                    <button className="idea-btn" title="✨ แนะนำไอเดีย" onClick={handleSuggestIdea} disabled={isInputDisabled}>💡</button>
                    <textarea
                        className="message-input"
                        placeholder={placeholders[currentMode] || "พิมพ์ข้อความของคุณที่นี่..."}
                        rows="1"
                        value={inputValue}
                        onChange={(e) => setInputValue(e.target.value)}
                        onKeyDown={(e) => { if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); onSendMessage(); } }}
                        disabled={isInputDisabled}
                    />
                    <button className="send-btn" onClick={onSendMessage} disabled={isInputDisabled}>
                        สร้าง {icons.send}
                    </button>
                </div>
            </div>
        </div>
    );
};

const Message = ({ message, activeCharacterForChat, currentMode, callApi, addMessageToHistory, userId }) => {
    
    const [generatingImage, setGeneratingImage] = useState(false);
    const [generatedImageUrl, setGeneratedImageUrl] = useState(null);

    const handleGenerateImage = async (prompt) => {
        setGeneratingImage(true);
        const negativePrompt = document.getElementById('negativePromptInput')?.value.trim() || '';
        const finalPrompt = prompt + (negativePrompt ? ` --no ${negativePrompt}` : '');
        
        const payload = { instances: [{ prompt: finalPrompt }], parameters: { "sampleCount": 1 } };
        const result = await callApi(IMAGEN_API_URL, payload);
        
        setGeneratingImage(false);
        if (result.error || !result.predictions?.[0]?.bytesBase64Encoded) {
             console.error("Image generation failed");
        } else {
             const base64Data = result.predictions[0].bytesBase64Encoded;
             const imageUrl = `data:image/png;base64,${base64Data}`;
             setGeneratedImageUrl(imageUrl);
        }
    };
    
    const handleSaveFavorite = async (prompt, button) => {
        await addDoc(collection(db, `users/${userId}/favorites`), {
            prompt, mode: currentMode, createdAt: new Date()
        });
        button.textContent = '⭐ บันทึกแล้ว!'; button.disabled = true;
        setTimeout(() => { button.textContent = '⭐ บันทึก'; button.disabled = false; }, 2000);
    };

    const handleSaveToGallery = async (imageUrl, prompt, button) => {
        await addDoc(collection(db, `users/${userId}/gallery`), {
            imageUrl, prompt, createdAt: new Date()
        });
        button.textContent = '💾 บันทึกแล้ว!'; button.disabled = true;
    };


    const { role, parts } = message;
    const text = parts?.[0]?.text;
    
    const avatarContent = () => {
        if (role === 'user') return '👤';
        if (role === 'assistant' && currentMode === 'char-chat' && activeCharacterForChat) {
            return activeCharacterForChat.imageUrl 
                ? <img src={activeCharacterForChat.imageUrl} alt={activeCharacterForChat.nickname} /> 
                : '🎭';
        }
        return '🤖';
    };

    const renderContent = () => {
        if (role === 'loading') {
            return <div className="typing-indicator"><span></span><span></span><span></span></div>;
        }
        if (role === 'assistant') {
            const promptText = text;
            const isCodeLike = ['promptmaster', 'image'].includes(currentMode);
            const contentHTML = { __html: isCodeLike ? `<pre><code>${text}</code></pre>` : text.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>').replace(/```([\s\S]*?)```/g, '<pre><code>$1</code></pre>').replace(/\n/g, '<br />') };

            return (
                <>
                    <div dangerouslySetInnerHTML={contentHTML} />
                    {generatingImage && 
                        <div className="image-loading-placeholder">
                            <div className="image-loading-spinner"></div><p>กำลังสร้างภาพ...</p>
                        </div>
                    }
                    {generatedImageUrl && 
                        <div>
                            <img src={generatedImageUrl} className="generated-image" alt="generated content"/>
                        </div>
                    }
                    <div className="message-actions">
                         <button className="action-btn" onClick={(e) => {
                            navigator.clipboard.writeText(promptText);
                            e.target.textContent = 'คัดลอกแล้ว!';
                            setTimeout(() => e.target.textContent = 'คัดลอก', 2000);
                         }}>คัดลอก</button>
                         <button className="action-btn" onClick={(e) => handleSaveFavorite(promptText, e.target)}>⭐ บันทึก</button>
                         {currentMode === 'image' && !generatedImageUrl && <button className="action-btn" onClick={() => handleGenerateImage(promptText)}>🎨 สร้างภาพ</button>}
                         {generatedImageUrl && <button className="action-btn" onClick={(e) => handleSaveToGallery(generatedImageUrl, promptText, e.target)}>💾 บันทึก</button>}
                    </div>
                </>
            );
        }
        return text;
    };

    return (
        <div className={`message ${role}`}>
            <div className="message-avatar">{avatarContent()}</div>
            <div className="message-content">{renderContent()}</div>
        </div>
    );
};

const StatusBar = ({ isLoading, onClearHistory }) => (
    <div className="status-bar">
        <div className="status-indicator">
            <span className={`status-dot ${!isLoading ? 'active' : ''}`}></span>
            <span>{isLoading ? 'AI กำลังประมวลผล...' : 'พร้อมใช้งาน'}</span>
        </div>
        <button onClick={onClearHistory} style={{ fontSize: '0.8rem', padding: '0.25rem 0.5rem', background: '#555' }}>
            ล้างประวัติ
        </button>
    </div>
);


const LoginModal = ({ setLoginModalOpen }) => {
    const [isLoginMode, setIsLoginMode] = useState(true);
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const [error, setError] = useState('');

    const handleSubmit = async (e) => {
        e.preventDefault();
        setError('');
        if (!email || !password) {
            setError('กรุณากรอกอีเมลและรหัสผ่าน');
            return;
        }
        try {
            if (isLoginMode) {
                await signInWithEmailAndPassword(auth, email, password);
            } else {
                await createUserWithEmailAndPassword(auth, email, password);
            }
            // onAuthStateChanged will handle closing the modal
        } catch (err) {
             let message = "เกิดข้อผิดพลาด กรุณาลองใหม่";
             switch (err.code) {
                case 'auth/wrong-password': message = 'รหัสผ่านไม่ถูกต้อง'; break;
                case 'auth/user-not-found': message = 'ไม่พบผู้ใช้นี้'; break;
                case 'auth/email-already-in-use': message = 'อีเมลนี้ถูกใช้งานแล้ว'; break;
                case 'auth/weak-password': message = 'รหัสผ่านต้องมีความยาวอย่างน้อย 6 ตัวอักษร'; break;
             }
            setError(message);
        }
    };
    
    return (
         <div id="loginModal" className="modal show">
            <div className="modal-content">
                <h2>{isLoginMode ? 'เข้าสู่ระบบ' : 'สมัครสมาชิก'}</h2>
                {error && <div style={{ color: 'var(--error-color)', marginBottom: '1rem', textAlign: 'center' }}>{error}</div>}
                <form onSubmit={handleSubmit}>
                    <div className="form-group">
                        <label htmlFor="email">อีเมล</label>
                        <input type="email" value={email} onChange={(e) => setEmail(e.target.value)} required />
                    </div>
                    <div className="form-group">
                        <label htmlFor="password">รหัสผ่าน</label>
                        <input type="password" value={password} onChange={(e) => setPassword(e.target.value)} required />
                    </div>
                    <button type="submit" style={{ width: '100%', padding: '0.75rem', background: 'var(--primary-gradient)', color: 'white', fontWeight: 600, border: 'none' }}>
                        {isLoginMode ? 'เข้าสู่ระบบ' : 'สมัครสมาชิก'}
                    </button>
                </form>
                 <p style={{ textAlign: 'center', marginTop: '1rem', color: 'var(--text-secondary-color)' }}>
                    <span>{isLoginMode ? 'ยังไม่มีบัญชี?' : 'มีบัญชีอยู่แล้ว?'}</span>
                    <a href="#" onClick={(e) => { e.preventDefault(); setIsLoginMode(!isLoginMode); setError(''); }}>
                        {isLoginMode ? 'สมัครสมาชิก' : 'เข้าสู่ระบบ'}
                    </a>
                </p>
            </div>
        </div>
    );
};

const CharacterModal = ({ character, onClose, userId }) => {
    const [formData, setFormData] = useState({
        imageUrl: character?.imageUrl || '',
        nickname: character?.nickname || '',
        appearance: character?.appearance || '',
        personality: character?.personality || '',
    });

    const handleChange = (e) => {
        const { id, value } = e.target;
        setFormData(prev => ({ ...prev, [id]: value }));
    };

    const handleSubmit = async (e) => {
        e.preventDefault();
        if (!formData.nickname || !formData.appearance) {
            alert('กรุณากรอก "ชื่อเรียก/บทบาท" และ "ลักษณะภายนอก"');
            return;
        }

        const characterData = { ...formData, createdAt: new Date() };

        try {
            if (character?.id) {
                await updateDoc(doc(db, `users/${userId}/characters`, character.id), characterData);
            } else {
                await addDoc(collection(db, `users/${userId}/characters`), characterData);
            }
            onClose();
        } catch (error) {
            console.error("Error saving character:", error);
            alert("เกิดข้อผิดพลาดในการบันทึก");
        }
    };

    return (
        <div className="modal show">
            <div className="modal-content">
                <span className="close-btn" onClick={onClose}>&times;</span>
                <h2>🧙 สร้าง/แก้ไข ตัวละคร</h2>
                <form onSubmit={handleSubmit} style={{overflowY: 'auto', paddingRight: '1rem'}}>
                    <div className="form-group">
                        <label>ลิงก์รูปโปรไฟล์ (Profile Image URL)</label>
                        <input type="url" id="imageUrl" value={formData.imageUrl} onChange={handleChange} placeholder="วาง URL ของรูปภาพที่นี่" />
                    </div>
                    <div className="form-group">
                        <label>ชื่อเรียก/บทบาท*</label>
                        <input type="text" id="nickname" value={formData.nickname} onChange={handleChange} required placeholder='เช่น "CEO สาว", "นักสืบพเนจร"' />
                    </div>
                    <div className="form-group">
                        <label>รายละเอียดลักษณะภายนอก*</label>
                        <textarea id="appearance" rows="4" value={formData.appearance} onChange={handleChange} required placeholder="เช่น หญิงสาวอายุ 25 ปี, ผมสีดำยาว, ตาสีน้ำตาล..."></textarea>
                    </div>
                    <div className="form-group">
                        <label>บุคลิกและนิสัย</label>
                        <textarea id="personality" rows="3" value={formData.personality} onChange={handleChange} placeholder="เช่น มั่นใจ, ฉลาด, อบอุ่น..."></textarea>
                    </div>
                    <button type="submit" style={{width: '100%', padding: '0.75rem', background: 'var(--primary-gradient)', color: 'white', fontWeight: 600, border: 'none'}}>บันทึกตัวละคร</button>
                </form>
            </div>
        </div>
    );
};

const ImageViewerModal = ({ src, prompt, onClose }) => {
    return (
        <div className="modal show" id="image-viewer-modal" onClick={onClose}>
            <span className="close-btn" style={{ color: 'white', fontSize: '40px' }}>&times;</span>
            <div className="modal-content" onClick={e => e.stopPropagation()}>
                <div className="image-viewer-container">
                    <img src={src} alt={prompt} />
                    <div className="image-viewer-prompt">{prompt}</div>
                </div>
            </div>
        </div>
    );
};


export default App;
