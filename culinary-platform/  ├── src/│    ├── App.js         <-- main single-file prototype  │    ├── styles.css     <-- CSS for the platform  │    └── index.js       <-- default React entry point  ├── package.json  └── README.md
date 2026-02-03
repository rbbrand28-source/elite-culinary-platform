// App.js — Full Single-File Prototype
import React, { useState, useEffect } from "react";
import { initializeApp } from "firebase/app";
import { getFirestore, collection, addDoc } from "firebase/firestore";
import "./styles.css";

// ---------------- FIREBASE CONFIG ----------------
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// ---------------- APP COMPONENT ----------------
function App() {
  const [stage, setStage] = useState(0);
  const [userStory, setUserStory] = useState("");
  const [feed, setFeed] = useState([]);
  const [identityScore, setIdentityScore] = useState(0);

  // Stage0 → Stage1
  const handleStage0Complete = async (story) => {
    if (story.trim() === "") {
      alert("Please share your culinary story to continue.");
      return;
    }
    setUserStory(story);

    // Store in Firebase
    try {
      await addDoc(collection(db, "users"), {
        story: story,
        timestamp: new Date()
      });
    } catch (e) {
      console.error("Firebase error:", e);
    }

    // Simulate AI feed & identity score
    setFeed([
      `🍷 Suggested pairing based on "${story}": Pinot Noir`,
      `🍝 Exclusive dinner event matching your taste`,
      `🥗 Badge unlocked: Grandma’s Secret Salad`
    ]);
    setIdentityScore(Math.floor(Math.random() * 100) + 1);

    setStage(1);
  };

  // Stage1 → Stage2
  const handleStage1Complete = () => {
    setStage(2);
  };

  // ---------------- STAGE COMPONENTS ----------------
  const Stage0 = () => {
    return (
      <div className="full-screen">
        <h1>What meal changed your life?</h1>
        <p>Share a story, taste, or ritual — we’ll understand your culinary identity.</p>
        <textarea
          value={userStory}
          onChange={(e) => setUserStory(e.target.value)}
          rows={5}
          cols={40}
          placeholder="Start typing..."
        />
        <button className="btn" onClick={() => handleStage0Complete(userStory)}>Continue</button>
      </div>
    );
  };

  const Stage1 = () => {
    return (
      <div className="feed">
        <h1>Your Culinary Narrative</h1>
        {feed.map((item, idx) => (
          <div key={idx} className="feed-item">{item}</div>
        ))}
        <div className="identity-score">Identity Score: {identityScore} / 100</div>
        <button className="btn" onClick={handleStage1Complete}>Request Access</button>
      </div>
    );
  };

  const Stage2 = () => {
    return (
      <div className="feed">
        <h1>Book Your Exclusive Experience</h1>
        <p>Only available after we understand your culinary identity.</p>
        <a href="mailto:rbbrand28@gmail.com" className="btn">Request Access</a>
      </div>
    );
  };

  return (
    <div>
      {stage === 0 && <Stage0 />}
      {stage === 1 && <Stage1 />}
      {stage === 2 && <Stage2 />}
    </div>
  );
}

export default App;

body {margin:0;font-family:Arial,sans-serif;background:#0b0b0b;color:#fff;}
.full-screen {height:100vh;display:flex;align-items:center;justify-content:center;flex-direction:column;text-align:center;padding:20px;}
h1{font-size:48px;margin-bottom:20px;}
p{color:#ccc;max-width:700px;margin:10px auto;}
textarea {padding:10px;border-radius:8px;border:none;width:80%;max-width:600px;}
.btn{padding:15px 40px;background:#fff;color:#000;text-decoration:none;border-radius:30px;font-weight:bold;margin-top:20px;cursor:pointer;border:none;}
.feed{padding:40px;max-width:900px;margin:auto;}
.feed-item{background:#111;margin:20px 0;padding:20px;border-radius:12px;border:1px solid #222;}
.identity-score{margin-top:20px;font-size:16px;color:#b5b5b5;}
