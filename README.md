import { useState, useEffect, useRef } from "react";

const CATEGORIES = ["All", "Gaming", "Music", "Live", "Tech", "Vlogs", "News", "Sports", "Cooking", "Travel"];

const MOCK_VIDEOS = Array.from({ length: 40 }, (_, i) => ({
  id: i + 1,
  title: [
    "Building a $50,000 Gaming PC Setup in 2025 🔥",
    "I Spent 24 Hours Coding a Full Stack App",
    "The Most Satisfying Music Production Session",
    "Top 10 Travel Destinations You Must Visit",
    "How I Gained 1M Subscribers in 30 Days",
    "Cooking the World's Most Expensive Dish",
    "Breaking: AI Changes Everything We Knew",
    "Epic Mountain Biking Through the Alps",
    "React 19 New Features - Complete Guide",
    "Street Food Tour - Tokyo Night Markets",
    "Building a Drone From Scratch - Full Guide",
    "AI Art: Creating Masterpieces in Minutes",
    "The Ultimate Home Gym Setup 2025",
    "Deep Sea Diving with Sharks - No Cage!",
    "Minimalist Living - I Sold Everything",
    "World's Fastest Typist Breaks Record Again",
    "Behind the Scenes: How Movies Are Made",
    "Crypto in 2025 - What Nobody Tells You",
    "Urban Farming - Growing Food in my Apartment",
    "F1 Simulator vs Real Driver Challenge",
  ][i % 20],
  channel: ["TechVault", "CodeWithMe", "BeatMaker Pro", "WanderlustTV", "GrowthHacks", "ChefElite", "NewsFlash", "XtremeRides", "DevMaster", "FoodieWorld"][i % 10],
  views: `${(Math.random() * 9.9 + 0.1).toFixed(1)}M`,
  time: `${Math.floor(Math.random() * 11) + 1}:${String(Math.floor(Math.random() * 59)).padStart(2, "0")}`,
  uploaded: ["2h ago", "5h ago", "1d ago", "3d ago", "1w ago", "2w ago"][i % 6],
  likes: `${(Math.random() * 99 + 1).toFixed(0)}K`,
  category: CATEGORIES[Math.floor(Math.random() * (CATEGORIES.length - 1)) + 1],
  thumbnail: `hsl(${(i * 37) % 360}, 60%, 35%)`,
  avatar: `hsl(${(i * 73) % 360}, 70%, 50%)`,
  verified: i % 3 === 0,
  isLive: i % 8 === 0,
  watchLater: false,
}));

const PlayIcon = () => (
  <svg viewBox="0 0 24 24" fill="currentColor" width="24" height="24">
    <path d="M8 5v14l11-7z" />
  </svg>
);

const HomeIcon = ({ active }) => (
  <svg viewBox="0 0 24 24" fill={active ? "currentColor" : "none"} stroke="currentColor" strokeWidth="2" width="24" height="24">
    <path d="M3 9l9-7 9 7v11a2 2 0 01-2 2H5a2 2 0 01-2-2z" />
    <polyline points="9,22 9,12 15,12 15,22" />
  </svg>
);

const SearchIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" width="20" height="20">
    <circle cx="11" cy="11" r="8" /><line x1="21" y1="21" x2="16.65" y2="16.65" />
  </svg>
);

const UploadIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" width="22" height="22">
    <path d="M21 15v4a2 2 0 01-2 2H5a2 2 0 01-2-2v-4" />
    <polyline points="17,8 12,3 7,8" />
    <line x1="12" y1="3" x2="12" y2="15" />
  </svg>
);

const BellIcon = ({ active }) => (
  <svg viewBox="0 0 24 24" fill={active ? "currentColor" : "none"} stroke="currentColor" strokeWidth="2" width="22" height="22">
    <path d="M18 8A6 6 0 006 8c0 7-3 9-3 9h18s-3-2-3-9" />
    <path d="M13.73 21a2 2 0 01-3.46 0" />
  </svg>
);

const ShortsIcon = ({ active }) => (
  <svg viewBox="0 0 24 24" fill={active ? "currentColor" : "none"} stroke="currentColor" strokeWidth="2" width="22" height="22">
    <rect x="3" y="2" width="18" height="20" rx="3" />
    <path d="M10 8l6 4-6 4V8z" fill="currentColor" stroke="none" />
  </svg>
);

const LibraryIcon = ({ active }) => (
  <svg viewBox="0 0 24 24" fill={active ? "currentColor" : "none"} stroke="currentColor" strokeWidth="2" width="22" height="22">
    <path d="M22 19a2 2 0 01-2 2H4a2 2 0 01-2-2V5a2 2 0 012-2h5l2 3h9a2 2 0 012 2z" />
  </svg>
);

const LikeIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" width="18" height="18">
    <path d="M14 9V5a3 3 0 00-3-3l-4 9v11h11.28a2 2 0 002-1.7l1.38-9a2 2 0 00-2-2.3H14z" />
    <path d="M7 22H4a2 2 0 01-2-2v-7a2 2 0 012-2h3" />
  </svg>
);

const MenuDotsIcon = () => (
  <svg viewBox="0 0 24 24" fill="currentColor" width="18" height="18">
    <circle cx="12" cy="5" r="1.5" /><circle cx="12" cy="12" r="1.5" /><circle cx="12" cy="19" r="1.5" />
  </svg>
);

const ClockIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" width="16" height="16">
    <circle cx="12" cy="12" r="10" /><polyline points="12,6 12,12 16,14" />
  </svg>
);

const FireIcon = () => (
  <svg viewBox="0 0 24 24" fill="currentColor" width="14" height="14">
    <path d="M17.66 11.2C17.43 10.9 17.15 10.64 16.89 10.38C16.22 9.78 15.46 9.35 14.82 8.72C13.33 7.26 13 4.85 13.95 3C13 3.23 12.17 3.75 11.46 4.32C8.87 6.4 7.85 10.07 9.07 13.22C9.11 13.32 9.15 13.42 9.15 13.55C9.15 13.77 9 13.97 8.8 14.05C8.57 14.15 8.33 14.09 8.14 13.93C8.08 13.88 8.04 13.83 8 13.76C6.87 12.33 6.69 10.28 7.45 8.64C5.78 10 4.87 12.3 5 14.47C5.06 14.97 5.12 15.47 5.29 15.97C5.43 16.57 5.7 17.17 6 17.7C7.08 19.43 8.95 20.67 10.96 20.92C13.1 21.19 15.39 20.8 17.03 19.32C18.86 17.66 19.5 15 18.56 12.72L18.43 12.46C18.22 12 17.66 11.2 17.66 11.2Z" />
  </svg>
);

const VerifyIcon = () => (
  <svg viewBox="0 0 24 24" fill="#3b82f6" width="14" height="14">
    <path d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" stroke="#3b82f6" strokeWidth="2" fill="none" strokeLinecap="round" strokeLinejoin="round" />
  </svg>
);

const UploadModal = ({ onClose }) => {
  const [step, setStep] = useState(1);
  const [dragOver, setDragOver] = useState(false);
  const [uploading, setUploading] = useState(false);
  const [progress, setProgress] = useState(0);
  const [title, setTitle] = useState("");
  const [desc, setDesc] = useState("");
  const [selectedFile, setSelectedFile] = useState(null);

  const handleUpload = () => {
    if (!title) return;
    setUploading(true);
    let p = 0;
    const interval = setInterval(() => {
      p += Math.random() * 12;
      if (p >= 100) { p = 100; clearInterval(interval); setStep(3); setUploading(false); }
      setProgress(Math.min(p, 100));
    }, 200);
  };

  return (
    <div style={{ position: "fixed", inset: 0, background: "rgba(0,0,0,0.85)", zIndex: 100, display: "flex", alignItems: "flex-end", justifyContent: "center", backdropFilter: "blur(4px)" }}>
      <div style={{ background: "#0f0f0f", borderRadius: "24px 24px 0 0", width: "100%", maxWidth: 430, padding: 24, border: "1px solid #1e1e1e", borderBottom: "none", animation: "slideUp 0.3s ease" }}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 20 }}>
          <span style={{ fontFamily: "'Sora', sans-serif", fontSize: 18, fontWeight: 700, color: "#fff" }}>
            {step === 1 ? "Upload Video" : step === 2 ? "Video Details" : "🎉 Published!"}
          </span>
          <button onClick={onClose} style={{ background: "#1e1e1e", border: "none", color: "#aaa", width: 32, height: 32, borderRadius: "50%", cursor: "pointer", fontSize: 16, display: "flex", alignItems: "center", justifyContent: "center" }}>✕</button>
        </div>

        {step === 1 && (
          <div>
            <div
              onDragOver={(e) => { e.preventDefault(); setDragOver(true); }}
              onDragLeave={() => setDragOver(false)}
              onDrop={(e) => { e.preventDefault(); setDragOver(false); setSelectedFile("video.mp4"); setStep(2); }}
              style={{ border: `2px dashed ${dragOver ? "#ff4444" : "#333"}`, borderRadius: 16, padding: "40px 20px", textAlign: "center", background: dragOver ? "#1a0000" : "#111", cursor: "pointer", transition: "all 0.2s" }}
              onClick={() => { setSelectedFile("my_video.mp4"); setStep(2); }}
            >
              <div style={{ fontSize: 48, marginBottom: 12 }}>📹</div>
              <p style={{ color: "#fff", fontFamily: "'Sora', sans-serif", fontWeight: 600, marginBottom: 6 }}>Drop your video here</p>
              <p style={{ color: "#666", fontSize: 13 }}>MP4, AVI, MOV, MKV • Up to 50GB</p>
              <div style={{ marginTop: 16, background: "#ff4444", color: "#fff", border: "none", borderRadius: 20, padding: "10px 24px", fontSize: 14, fontWeight: 600, display: "inline-block", fontFamily: "'Sora', sans-serif" }}>Select File</div>
            </div>
            <div style={{ display: "flex", gap: 8, marginTop: 16, flexWrap: "wrap" }}>
              {["4K Supported", "HDR Ready", "Auto-Captions", "CDN Upload"].map(f => (
                <span key={f} style={{ background: "#1a1a1a", color: "#666", fontSize: 11, padding: "4px 10px", borderRadius: 20, border: "1px solid #2a2a2a", fontFamily: "'Sora', sans-serif" }}>✓ {f}</span>
              ))}
            </div>
          </div>
        )}

        {step === 2 && (
          <div>
            <div style={{ background: "#111", borderRadius: 12, padding: 12, display: "flex", alignItems: "center", gap: 10, marginBottom: 20, border: "1px solid #2a2a2a" }}>
              <span style={{ fontSize: 24 }}>🎬</span>
              <div>
                <div style={{ color: "#fff", fontSize: 13, fontWeight: 600, fontFamily: "'Sora', sans-serif" }}>{selectedFile}</div>
                <div style={{ color: "#666", fontSize: 11 }}>Ready to upload</div>
              </div>
            </div>
            <input value={title} onChange={e => setTitle(e.target.value)} placeholder="Video title *" style={{ width: "100%", background: "#1a1a1a", border: `1px solid ${title ? "#ff4444" : "#2a2a2a"}`, borderRadius: 10, padding: "12px 14px", color: "#fff", fontSize: 14, fontFamily: "'Sora', sans-serif", marginBottom: 12, boxSizing: "border-box", outline: "none" }} />
            <textarea value={desc} onChange={e => setDesc(e.target.value)} placeholder="Description (optional)" rows={3} style={{ width: "100%", background: "#1a1a1a", border: "1px solid #2a2a2a", borderRadius: 10, padding: "12px 14px", color: "#fff", fontSize: 13, fontFamily: "'Sora', sans-serif", resize: "none", boxSizing: "border-box", outline: "none", marginBottom: 16 }} />

            {uploading && (
              <div style={{ marginBottom: 16 }}>
                <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 6 }}>
                  <span style={{ color: "#aaa", fontSize: 12, fontFamily: "'Sora', sans-serif" }}>Uploading to CDN...</span>
                  <span style={{ color: "#ff4444", fontSize: 12, fontFamily: "'Sora', sans-serif" }}>{Math.floor(progress)}%</span>
                </div>
                <div style={{ background: "#1a1a1a", borderRadius: 4, height: 4 }}>
                  <div style={{ background: "linear-gradient(90deg, #ff4444, #ff8800)", height: "100%", borderRadius: 4, width: `${progress}%`, transition: "width 0.2s" }} />
                </div>
              </div>
            )}

            <button onClick={handleUpload} disabled={!title || uploading} style={{ width: "100%", background: title && !uploading ? "linear-gradient(135deg, #ff4444, #ff8800)" : "#222", color: title && !uploading ? "#fff" : "#555", border: "none", borderRadius: 12, padding: "14px", fontSize: 15, fontWeight: 700, fontFamily: "'Sora', sans-serif", cursor: title && !uploading ? "pointer" : "not-allowed" }}>
              {uploading ? "Uploading..." : "🚀 Publish Video"}
            </button>
          </div>
        )}

        {step === 3 && (
          <div style={{ textAlign: "center", padding: "20px 0" }}>
            <div style={{ fontSize: 64, marginBottom: 16 }}>🎉</div>
            <p style={{ color: "#fff", fontFamily: "'Sora', sans-serif", fontSize: 18, fontWeight: 700, marginBottom: 8 }}>Video Published!</p>
            <p style={{ color: "#666", fontSize: 13, marginBottom: 24 }}>Your video is live and being processed across our CDN network. It joins today's 50,000+ uploads.</p>
            <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10, marginBottom: 20 }}>
              {[["Processing", "2 mins"], ["CDN Nodes", "142"], ["Quality", "4K HDR"], ["Status", "Live ✓"]].map(([k, v]) => (
                <div key={k} style={{ background: "#111", borderRadius: 10, padding: 12, border: "1px solid #1e1e1e" }}>
                  <div style={{ color: "#666", fontSize: 11, fontFamily: "'Sora', sans-serif" }}>{k}</div>
                  <div style={{ color: "#ff4444", fontSize: 14, fontWeight: 700, fontFamily: "'Sora', sans-serif" }}>{v}</div>
                </div>
              ))}
            </div>
            <button onClick={onClose} style={{ width: "100%", background: "linear-gradient(135deg, #ff4444, #ff8800)", color: "#fff", border: "none", borderRadius: 12, padding: "14px", fontSize: 15, fontWeight: 700, fontFamily: "'Sora', sans-serif", cursor: "pointer" }}>Done</button>
          </div>
        )}
      </div>
    </div>
  );
};

const VideoPlayer = ({ video, onClose }) => {
  const [liked, setLiked] = useState(false);
  const [saved, setSaved] = useState(false);
  const [playing, setPlaying] = useState(true);
  const [showComments, setShowComments] = useState(false);

  const comments = [
    { user: "Alex M.", text: "This is insane! 🔥", time: "2h", likes: 234 },
    { user: "Sarah K.", text: "Been waiting for this video!", time: "5h", likes: 89 },
    { user: "DevGuru", text: "Incredible quality as always 👌", time: "1d", likes: 456 },
    { user: "NightOwl99", text: "The editing on this is top tier", time: "2d", likes: 122 },
  ];

  return (
    <div style={{ position: "fixed", inset: 0, background: "#000", zIndex: 50, overflowY: "auto", maxWidth: 430, margin: "0 auto" }}>
      {/* Video Area */}
      <div style={{ position: "relative", background: video.thumbnail, aspectRatio: "16/9", display: "flex", alignItems: "center", justifyContent: "center" }}>
        <button onClick={onClose} style={{ position: "absolute", top: 12, left: 12, background: "rgba(0,0,0,0.6)", border: "none", color: "#fff", width: 36, height: 36, borderRadius: "50%", cursor: "pointer", fontSize: 16, display: "flex", alignItems: "center", justifyContent: "center", zIndex: 10 }}>←</button>
        <button onClick={() => setPlaying(!playing)} style={{ background: "rgba(0,0,0,0.5)", border: "2px solid rgba(255,255,255,0.3)", color: "#fff", width: 64, height: 64, borderRadius: "50%", cursor: "pointer", display: "flex", alignItems: "center", justifyContent: "center" }}>
          {playing ? <span style={{ fontSize: 20 }}>⏸</span> : <PlayIcon />}
        </button>
        {video.isLive && <span style={{ position: "absolute", top: 12, right: 12, background: "#ff4444", color: "#fff", fontSize: 11, fontWeight: 700, padding: "3px 8px", borderRadius: 4, fontFamily: "'Sora', sans-serif" }}>● LIVE</span>}
        {/* Scrubber */}
        <div style={{ position: "absolute", bottom: 0, left: 0, right: 0, padding: "8px 12px" }}>
          <div style={{ background: "rgba(255,255,255,0.2)", borderRadius: 2, height: 3, position: "relative" }}>
            <div style={{ background: "#ff4444", height: "100%", width: "42%", borderRadius: 2 }} />
            <div style={{ position: "absolute", top: "50%", left: "42%", transform: "translate(-50%, -50%)", width: 12, height: 12, background: "#ff4444", borderRadius: "50%" }} />
          </div>
          <div style={{ display: "flex", justifyContent: "space-between", marginTop: 4 }}>
            <span style={{ color: "rgba(255,255,255,0.8)", fontSize: 11 }}>4:23</span>
            <span style={{ color: "rgba(255,255,255,0.5)", fontSize: 11 }}>{video.time}</span>
          </div>
        </div>
      </div>

      {/* Info */}
      <div style={{ padding: "16px 16px 0" }}>
        <h2 style={{ color: "#fff", fontFamily: "'Sora', sans-serif", fontSize: 16, fontWeight: 700, lineHeight: 1.4, marginBottom: 8 }}>{video.title}</h2>
        <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 14 }}>
          <span style={{ color: "#aaa", fontSize: 13 }}>{video.views} views</span>
          <span style={{ color: "#444" }}>•</span>
          <span style={{ color: "#aaa", fontSize: 13 }}>{video.uploaded}</span>
        </div>

        {/* Action Bar */}
        <div style={{ display: "flex", gap: 8, marginBottom: 16, overflowX: "auto", paddingBottom: 4 }}>
          {[
            { icon: liked ? "👍" : "👍", label: liked ? video.likes : video.likes, action: () => setLiked(!liked), active: liked },
            { icon: "👎", label: "Dislike", action: () => {}, active: false },
            { icon: "↗️", label: "Share", action: () => {}, active: false },
            { icon: saved ? "🔖" : "🔖", label: saved ? "Saved" : "Save", action: () => setSaved(!saved), active: saved },
            { icon: "⬇️", label: "Download", action: () => {}, active: false },
          ].map(({ icon, label, action, active }) => (
            <button key={label} onClick={action} style={{ background: active ? "#1a0000" : "#1a1a1a", border: `1px solid ${active ? "#ff4444" : "#2a2a2a"}`, color: active ? "#ff4444" : "#ccc", borderRadius: 20, padding: "8px 14px", cursor: "pointer", display: "flex", alignItems: "center", gap: 5, fontSize: 13, fontFamily: "'Sora', sans-serif", whiteSpace: "nowrap", flexShrink: 0 }}>
              <span style={{ fontSize: 14 }}>{icon}</span>{label}
            </button>
          ))}
        </div>

        {/* Channel */}
        <div style={{ display: "flex", alignItems: "center", justifyContent: "space-between", padding: "12px 0", borderTop: "1px solid #1a1a1a", borderBottom: "1px solid #1a1a1a", marginBottom: 16 }}>
          <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
            <div style={{ width: 40, height: 40, borderRadius: "50%", background: video.avatar, display: "flex", alignItems: "center", justifyContent: "center", color: "#fff", fontWeight: 700, fontFamily: "'Sora', sans-serif" }}>{video.channel[0]}</div>
            <div>
              <div style={{ display: "flex", alignItems: "center", gap: 4 }}>
                <span style={{ color: "#fff", fontFamily: "'Sora', sans-serif", fontWeight: 600, fontSize: 14 }}>{video.channel}</span>
                {video.verified && <VerifyIcon />}
              </div>
              <span style={{ color: "#666", fontSize: 12 }}>2.4M subscribers</span>
            </div>
          </div>
          <button style={{ background: "#fff", color: "#000", border: "none", borderRadius: 20, padding: "8px 16px", fontWeight: 700, fontSize: 13, fontFamily: "'Sora', sans-serif", cursor: "pointer" }}>Subscribe</button>
        </div>

        {/* Comments Toggle */}
        <button onClick={() => setShowComments(!showComments)} style={{ width: "100%", background: "#111", border: "1px solid #2a2a2a", borderRadius: 12, padding: "12px 16px", color: "#fff", cursor: "pointer", display: "flex", justifyContent: "space-between", alignItems: "center", fontFamily: "'Sora', sans-serif", fontSize: 14, marginBottom: showComments ? 12 : 24 }}>
          <span>💬 Comments (12.4K)</span><span style={{ color: "#666" }}>{showComments ? "▲" : "▼"}</span>
        </button>

        {showComments && (
          <div style={{ marginBottom: 24 }}>
            <div style={{ display: "flex", gap: 10, marginBottom: 16 }}>
              <div style={{ width: 32, height: 32, borderRadius: "50%", background: "#ff4444", flexShrink: 0 }} />
              <input placeholder="Add a comment..." style={{ flex: 1, background: "#1a1a1a", border: "1px solid #2a2a2a", borderRadius: 20, padding: "8px 14px", color: "#fff", fontSize: 13, fontFamily: "'Sora', sans-serif", outline: "none" }} />
            </div>
            {comments.map((c, i) => (
              <div key={i} style={{ display: "flex", gap: 10, marginBottom: 14 }}>
                <div style={{ width: 32, height: 32, borderRadius: "50%", background: `hsl(${i * 90}, 60%, 50%)`, flexShrink: 0, display: "flex", alignItems: "center", justifyContent: "center", color: "#fff", fontSize: 12, fontWeight: 700, fontFamily: "'Sora', sans-serif" }}>{c.user[0]}</div>
                <div>
                  <div style={{ display: "flex", gap: 8, alignItems: "center", marginBottom: 3 }}>
                    <span style={{ color: "#ccc", fontSize: 13, fontWeight: 600, fontFamily: "'Sora', sans-serif" }}>{c.user}</span>
                    <span style={{ color: "#555", fontSize: 11 }}>{c.time}</span>
                  </div>
                  <p style={{ color: "#aaa", fontSize: 13, marginBottom: 5, lineHeight: 1.4 }}>{c.text}</p>
                  <div style={{ display: "flex", gap: 12 }}>
                    <span style={{ color: "#666", fontSize: 12, cursor: "pointer" }}>👍 {c.likes}</span>
                    <span style={{ color: "#666", fontSize: 12, cursor: "pointer" }}>Reply</span>
                  </div>
                </div>
              </div>
            ))}
          </div>
        )}
      </div>
    </div>
  );
};

const StatsBar = () => {
  const [count, setCount] = useState(34287);
  useEffect(() => {
    const interval = setInterval(() => setCount(c => c + Math.floor(Math.random() * 3) + 1), 1500);
    return () => clearInterval(interval);
  }, []);

  return (
    <div style={{ background: "linear-gradient(135deg, #0d0d0d, #1a0808)", border: "1px solid #2a1a1a", borderRadius: 12, margin: "0 12px 12px", padding: "10px 14px", display: "flex", alignItems: "center", justifyContent: "space-between" }}>
      <div style={{ display: "flex", alignItems: "center", gap: 6 }}>
        <FireIcon />
        <span style={{ color: "#ff6644", fontSize: 11, fontFamily: "'Sora', sans-serif", fontWeight: 600 }}>Today's Uploads</span>
      </div>
      <div style={{ display: "flex", alignItems: "center", gap: 6 }}>
        <span style={{ color: "#ff4444", fontFamily: "'Sora', sans-serif", fontWeight: 800, fontSize: 15, fontVariantNumeric: "tabular-nums" }}>{count.toLocaleString()}</span>
        <span style={{ color: "#555", fontSize: 11 }}>/ 50,000</span>
        <div style={{ width: 50, height: 4, background: "#1a1a1a", borderRadius: 2, overflow: "hidden" }}>
          <div style={{ background: "linear-gradient(90deg, #ff4444, #ff8800)", height: "100%", width: `${(count / 50000) * 100}%`, borderRadius: 2, transition: "width 0.5s" }} />
        </div>
      </div>
    </div>
  );
};

export default function StreamVault() {
  const [activeTab, setActiveTab] = useState("home");
  const [activeCategory, setActiveCategory] = useState("All");
  const [searchQuery, setSearchQuery] = useState("");
  const [searching, setSearching] = useState(false);
  const [showUpload, setShowUpload] = useState(false);
  const [activeVideo, setActiveVideo] = useState(null);
  const [notifications, setNotifications] = useState(3);

  const filteredVideos = MOCK_VIDEOS.filter(v => {
    const matchCat = activeCategory === "All" || v.category === activeCategory;
    const matchSearch = !searchQuery || v.title.toLowerCase().includes(searchQuery.toLowerCase()) || v.channel.toLowerCase().includes(searchQuery.toLowerCase());
    return matchCat && matchSearch;
  });

  const shortsVideos = MOCK_VIDEOS.filter((_, i) => i % 4 === 0);

  return (
    <>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Sora:wght@400;500;600;700;800&display=swap');
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { background: #000; }
        ::-webkit-scrollbar { width: 0; height: 0; }
        @keyframes slideUp { from { transform: translateY(100%); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.5; } }
        .video-card:hover { transform: scale(1.01); }
        .nav-btn:hover { background: #1a1a1a !important; }
      `}</style>

      <div style={{ background: "#000", minHeight: "100vh", maxWidth: 430, margin: "0 auto", fontFamily: "'Sora', sans-serif", position: "relative", overflowX: "hidden" }}>
        {/* Header */}
        <div style={{ position: "sticky", top: 0, zIndex: 40, background: "rgba(0,0,0,0.95)", backdropFilter: "blur(12px)", borderBottom: "1px solid #111" }}>
          <div style={{ display: "flex", alignItems: "center", justifyContent: "space-between", padding: "12px 16px 8px" }}>
            <div style={{ display: "flex", alignItems: "center", gap: 6 }}>
              <div style={{ background: "linear-gradient(135deg, #ff4444, #ff8800)", borderRadius: 8, width: 28, height: 28, display: "flex", alignItems: "center", justifyContent: "center" }}>
                <PlayIcon />
              </div>
              <span style={{ fontSize: 20, fontWeight: 800, color: "#fff", letterSpacing: -0.5 }}>StreamVault</span>
            </div>
            <div style={{ display: "flex", gap: 6 }}>
              <button onClick={() => setSearching(!searching)} style={{ background: "transparent", border: "none", color: "#ccc", cursor: "pointer", padding: 8, borderRadius: 8, display: "flex" }}><SearchIcon /></button>
              <button onClick={() => setShowUpload(true)} style={{ background: "transparent", border: "none", color: "#ccc", cursor: "pointer", padding: 8, borderRadius: 8, display: "flex" }}><UploadIcon /></button>
              <button onClick={() => setNotifications(0)} style={{ background: "transparent", border: "none", color: "#ccc", cursor: "pointer", padding: 8, borderRadius: 8, display: "flex", position: "relative" }}>
                <BellIcon active={notifications > 0} />
                {notifications > 0 && <span style={{ position: "absolute", top: 4, right: 4, background: "#ff4444", color: "#fff", fontSize: 9, width: 16, height: 16, borderRadius: "50%", display: "flex", alignItems: "center", justifyContent: "center", fontWeight: 700 }}>{notifications}</span>}
              </button>
            </div>
          </div>

          {/* Search Bar */}
          {searching && (
            <div style={{ padding: "0 16px 10px" }}>
              <div style={{ background: "#111", borderRadius: 24, padding: "10px 16px", display: "flex", alignItems: "center", gap: 10, border: "1px solid #2a2a2a" }}>
                <SearchIcon />
                <input autoFocus value={searchQuery} onChange={e => setSearchQuery(e.target.value)} placeholder="Search videos, channels..." style={{ flex: 1, background: "transparent", border: "none", color: "#fff", fontSize: 14, fontFamily: "'Sora', sans-serif", outline: "none" }} />
                {searchQuery && <button onClick={() => setSearchQuery("")} style={{ background: "none", border: "none", color: "#666", cursor: "pointer", fontSize: 16 }}>✕</button>}
              </div>
            </div>
          )}

          {/* Category Pills */}
          {activeTab === "home" && !searching && (
            <div style={{ display: "flex", gap: 8, padding: "0 16px 10px", overflowX: "auto" }}>
              {CATEGORIES.map(cat => (
                <button key={cat} onClick={() => setActiveCategory(cat)} style={{ background: activeCategory === cat ? "#fff" : "#1a1a1a", color: activeCategory === cat ? "#000" : "#ccc", border: "none", borderRadius: 20, padding: "6px 14px", fontSize: 13, fontWeight: 600, cursor: "pointer", whiteSpace: "nowrap", flexShrink: 0, fontFamily: "'Sora', sans-serif", transition: "all 0.15s" }}>
                  {cat}
                </button>
              ))}
            </div>
          )}
        </div>

        {/* Content */}
        <div style={{ paddingBottom: 80 }}>
          {activeTab === "home" && (
            <>
              <StatsBar />
              {filteredVideos.length === 0 ? (
                <div style={{ textAlign: "center", padding: "60px 20px", color: "#555" }}>
                  <div style={{ fontSize: 48, marginBottom: 12 }}>🔍</div>
                  <p style={{ fontFamily: "'Sora', sans-serif", fontSize: 16 }}>No videos found</p>
                </div>
              ) : (
                filteredVideos.map((video, idx) => (
                  <div key={video.id} className="video-card" onClick={() => setActiveVideo(video)} style={{ cursor: "pointer", marginBottom: 0, transition: "transform 0.15s", animation: `fadeIn 0.3s ease ${idx * 0.03}s both` }}>
                    {/* Thumbnail */}
                    <div style={{ position: "relative", background: video.thumbnail, aspectRatio: "16/9" }}>
                      <div style={{ position: "absolute", inset: 0, display: "flex", alignItems: "center", justifyContent: "center" }}>
                        <div style={{ width: 48, height: 48, borderRadius: "50%", background: "rgba(0,0,0,0.5)", display: "flex", alignItems: "center", justifyContent: "center", backdropFilter: "blur(4px)" }}>
                          <PlayIcon />
                        </div>
                      </div>
                      <div style={{ position: "absolute", bottom: 8, right: 8, background: "rgba(0,0,0,0.8)", color: "#fff", fontSize: 11, fontWeight: 700, padding: "2px 6px", borderRadius: 4 }}>{video.time}</div>
                      {video.isLive && (
                        <div style={{ position: "absolute", top: 8, left: 8, background: "#ff4444", color: "#fff", fontSize: 10, fontWeight: 700, padding: "3px 8px", borderRadius: 4, display: "flex", alignItems: "center", gap: 3 }}>
                          <span style={{ animation: "pulse 1s infinite", display: "inline-block", width: 6, height: 6, background: "#fff", borderRadius: "50%" }} />LIVE
                        </div>
                      )}
                    </div>

                    {/* Meta */}
                    <div style={{ padding: "10px 12px 14px", display: "flex", gap: 10 }}>
                      <div style={{ width: 36, height: 36, borderRadius: "50%", background: video.avatar, flexShrink: 0, display: "flex", alignItems: "center", justifyContent: "center", color: "#fff", fontWeight: 700, fontSize: 14 }}>{video.channel[0]}</div>
                      <div style={{ flex: 1, minWidth: 0 }}>
                        <p style={{ color: "#fff", fontSize: 14, fontWeight: 600, lineHeight: 1.35, marginBottom: 4, display: "-webkit-box", WebkitLineClamp: 2, WebkitBoxOrient: "vertical", overflow: "hidden" }}>{video.title}</p>
                        <div style={{ display: "flex", alignItems: "center", gap: 4, marginBottom: 2 }}>
                          <span style={{ color: "#aaa", fontSize: 12 }}>{video.channel}</span>
                          {video.verified && <VerifyIcon />}
                        </div>
                        <div style={{ display: "flex", gap: 4, color: "#666", fontSize: 12 }}>
                          <span>{video.views} views</span>
                          <span>•</span>
                          <span>{video.uploaded}</span>
                        </div>
                      </div>
                      <button onClick={e => { e.stopPropagation(); }} style={{ background: "none", border: "none", color: "#666", cursor: "pointer", padding: 4, alignSelf: "flex-start", flexShrink: 0 }}>
                        <MenuDotsIcon />
                      </button>
                    </div>
                  </div>
                ))
              )}
            </>
          )}

          {activeTab === "shorts" && (
            <div style={{ padding: 12 }}>
              <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 16 }}>
                <span style={{ fontSize: 20 }}>⚡</span>
                <span style={{ color: "#fff", fontWeight: 700, fontSize: 18 }}>Shorts</span>
                <span style={{ background: "#1a1a1a", color: "#ff4444", fontSize: 11, padding: "2px 8px", borderRadius: 10, border: "1px solid #2a2a2a" }}>NEW</span>
              </div>
              <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 8 }}>
                {shortsVideos.map((video, i) => (
                  <div key={video.id} onClick={() => setActiveVideo(video)} style={{ cursor: "pointer", borderRadius: 12, overflow: "hidden", position: "relative", aspectRatio: "9/16", background: video.thumbnail, animation: `fadeIn 0.3s ease ${i * 0.05}s both` }}>
                    <div style={{ position: "absolute", inset: 0, background: "linear-gradient(to top, rgba(0,0,0,0.8) 0%, transparent 50%)" }} />
                    <div style={{ position: "absolute", top: 8, right: 8, background: "rgba(0,0,0,0.5)", borderRadius: 4, padding: "2px 6px" }}>
                      <span style={{ color: "#fff", fontSize: 10, fontWeight: 700 }}>{video.time}</span>
                    </div>
                    <div style={{ position: "absolute", bottom: 8, left: 8, right: 8 }}>
                      <p style={{ color: "#fff", fontSize: 12, fontWeight: 600, lineHeight: 1.3, display: "-webkit-box", WebkitLineClamp: 2, WebkitBoxOrient: "vertical", overflow: "hidden" }}>{video.title}</p>
                      <span style={{ color: "#aaa", fontSize: 11 }}>{video.views} views</span>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          )}

          {activeTab === "library" && (
            <div style={{ padding: 16 }}>
              <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10, marginBottom: 24 }}>
                {[["🕐", "Watch Later", "12 videos"], ["📋", "Playlists", "5 lists"], ["❤️", "Liked", "89 videos"], ["📥", "Downloads", "24 videos"]].map(([icon, label, count]) => (
                  <div key={label} style={{ background: "#111", borderRadius: 14, padding: "16px 14px", border: "1px solid #1e1e1e", cursor: "pointer" }}>
                    <div style={{ fontSize: 24, marginBottom: 8 }}>{icon}</div>
                    <div style={{ color: "#fff", fontWeight: 700, fontSize: 14, marginBottom: 2 }}>{label}</div>
                    <div style={{ color: "#666", fontSize: 12 }}>{count}</div>
                  </div>
                ))}
              </div>

              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 12 }}>
                <span style={{ color: "#fff", fontWeight: 700, fontSize: 16 }}>Recent Activity</span>
                <span style={{ color: "#ff4444", fontSize: 13, cursor: "pointer" }}>View All</span>
              </div>
              {MOCK_VIDEOS.slice(0, 6).map((v) => (
                <div key={v.id} onClick={() => setActiveVideo(v)} style={{ display: "flex", gap: 10, marginBottom: 14, cursor: "pointer" }}>
                  <div style={{ width: 96, height: 54, borderRadius: 8, background: v.thumbnail, flexShrink: 0, display: "flex", alignItems: "center", justifyContent: "center" }}>
                    <div style={{ background: "rgba(0,0,0,0.4)", borderRadius: "50%", width: 28, height: 28, display: "flex", alignItems: "center", justifyContent: "center" }}>
                      <PlayIcon />
                    </div>
                  </div>
                  <div style={{ flex: 1 }}>
                    <p style={{ color: "#fff", fontSize: 13, fontWeight: 600, lineHeight: 1.35, marginBottom: 3, display: "-webkit-box", WebkitLineClamp: 2, WebkitBoxOrient: "vertical", overflow: "hidden" }}>{v.title}</p>
                    <span style={{ color: "#666", fontSize: 12 }}>{v.channel}</span>
                  </div>
                </div>
              ))}
            </div>
          )}

          {activeTab === "profile" && (
            <div style={{ padding: 16 }}>
              {/* Profile Header */}
              <div style={{ textAlign: "center", padding: "24px 0 28px" }}>
                <div style={{ width: 80, height: 80, borderRadius: "50%", background: "linear-gradient(135deg, #ff4444, #ff8800)", margin: "0 auto 12px", display: "flex", alignItems: "center", justifyContent: "center", fontSize: 32, fontWeight: 800, color: "#fff" }}>U</div>
                <h2 style={{ color: "#fff", fontSize: 20, fontWeight: 800, marginBottom: 4 }}>Your Channel</h2>
                <p style={{ color: "#666", fontSize: 14 }}>0 subscribers • 0 videos</p>
                <button onClick={() => setShowUpload(true)} style={{ marginTop: 16, background: "linear-gradient(135deg, #ff4444, #ff8800)", color: "#fff", border: "none", borderRadius: 20, padding: "10px 24px", fontSize: 14, fontWeight: 700, fontFamily: "'Sora', sans-serif", cursor: "pointer" }}>
                  + Upload Your First Video
                </button>
              </div>

              {/* Stats */}
              <div style={{ background: "#0d0d0d", borderRadius: 16, padding: 16, marginBottom: 20, border: "1px solid #1a1a1a" }}>
                <div style={{ color: "#aaa", fontSize: 12, marginBottom: 12, fontWeight: 600 }}>PLATFORM CAPACITY</div>
                <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 12 }}>
                  {[["50,000", "Daily Video Limit"], ["142", "CDN Nodes"], ["4K HDR", "Max Quality"], ["99.9%", "Uptime SLA"]].map(([val, label]) => (
                    <div key={label}>
                      <div style={{ color: "#ff4444", fontSize: 18, fontWeight: 800 }}>{val}</div>
                      <div style={{ color: "#555", fontSize: 11 }}>{label}</div>
                    </div>
                  ))}
                </div>
              </div>

              {/* Settings Menu */}
              {[["⚙️", "Account Settings"], ["🔔", "Notifications"], ["🎨", "Appearance"], ["🔒", "Privacy & Safety"], ["📊", "Analytics"], ["💎", "Upgrade to Pro"], ["ℹ️", "About StreamVault"]].map(([icon, label]) => (
                <div key={label} style={{ display: "flex", alignItems: "center", justifyContent: "space-between", padding: "14px 4px", borderBottom: "1px solid #111", cursor: "pointer" }}>
                  <div style={{ display: "flex", gap: 12, alignItems: "center" }}>
                    <span style={{ fontSize: 18 }}>{icon}</span>
                    <span style={{ color: label === "Upgrade to Pro" ? "#ff8800" : "#ccc", fontWeight: label === "Upgrade to Pro" ? 700 : 400, fontSize: 15 }}>{label}</span>
                  </div>
                  <span style={{ color: "#444" }}>›</span>
                </div>
              ))}
            </div>
          )}
        </div>

        {/* Bottom Nav */}
        <div style={{ position: "fixed", bottom: 0, left: "50%", transform: "translateX(-50%)", width: "100%", maxWidth: 430, background: "rgba(0,0,0,0.97)", backdropFilter: "blur(16px)", borderTop: "1px solid #1a1a1a", display: "flex", padding: "8px 0 16px", zIndex: 30 }}>
          {[
            { id: "home", label: "Home", Icon: HomeIcon },
            { id: "shorts", label: "Shorts", Icon: ShortsIcon },
            { id: "upload", label: "Upload", Icon: null },
            { id: "library", label: "Library", Icon: LibraryIcon },
            { id: "profile", label: "Profile", Icon: null },
          ].map(({ id, label, Icon }) => (
            <button key={id} className="nav-btn" onClick={() => id === "upload" ? setShowUpload(true) : setActiveTab(id)} style={{ flex: 1, background: "none", border: "none", cursor: "pointer", display: "flex", flexDirection: "column", alignItems: "center", gap: 3, padding: "4px 0", borderRadius: 8, transition: "background 0.15s" }}>
              {id === "upload" ? (
                <div style={{ width: 44, height: 32, background: "linear-gradient(135deg, #ff4444, #ff8800)", borderRadius: 10, display: "flex", alignItems: "center", justifyContent: "center" }}>
                  <UploadIcon />
                </div>
              ) : id === "profile" ? (
                <div style={{ width: 24, height: 24, borderRadius: "50%", background: activeTab === "profile" ? "linear-gradient(135deg, #ff4444, #ff8800)" : "#333", display: "flex", alignItems: "center", justifyContent: "center", fontSize: 13, fontWeight: 700, color: "#fff" }}>U</div>
              ) : (
                <div style={{ color: activeTab === id ? "#ff4444" : "#666" }}>
                  <Icon active={activeTab === id} />
                </div>
              )}
              <span style={{ fontSize: 10, fontFamily: "'Sora', sans-serif", fontWeight: 600, color: activeTab === id ? "#ff4444" : "#555" }}>{label}</span>
            </button>
          ))}
        </div>

        {/* Upload Modal */}
        {showUpload && <UploadModal onClose={() => setShowUpload(false)} />}

        {/* Video Player */}
        {activeVideo && <VideoPlayer video={activeVideo} onClose={() => setActiveVideo(null)} />}
      </div>
    </>
  );
}
