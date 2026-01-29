<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Soleminer | Game Portal</title>
    <!-- React & Babel for browser compilation -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap');
        
        body {
            font-family: 'Inter', sans-serif;
            background-color: #020617;
        }

        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.5);
            border-radius: 10px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: rgba(168, 85, 247, 0.2);
            border-radius: 10px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover {
            background: rgba(168, 85, 247, 0.4);
        }
        @keyframes fade-in {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .animate-in {
            animation: fade-in 0.6s cubic-bezier(0.22, 1, 0.36, 1) forwards;
        }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useCallback } = React;
        
        // Helper to render Lucide icons in React without complex imports
        const Icon = ({ name, size = 24, className = "", fill = "none" }) => {
            useEffect(() => {
                if (window.lucide) {
                    window.lucide.createIcons();
                }
            }, [name]);
            return <i data-lucide={name} style={{ width: size, height: size }} className={className}></i>;
        };

        const App = () => {
            const [projects, setProjects] = useState([]);
            const [loading, setLoading] = useState(true);
            const [selectedProject, setSelectedProject] = useState(null);
            const [stats, setStats] = useState({ totalViews: 0, totalLoves: 0, count: 0 });
            const [refreshing, setRefreshing] = useState(false);
            const [error, setError] = useState(null);

            const fetchWithRetry = async (url, retries = 3, backoff = 1000) => {
                const proxies = [
                    (u) => `https://api.allorigins.win/get?disableCache=true&url=${encodeURIComponent(u)}`,
                    (u) => `https://corsproxy.io/?${encodeURIComponent(u)}`
                ];

                for (let i = 0; i < retries; i++) {
                    for (const getProxyUrl of proxies) {
                        try {
                            const proxyUrl = getProxyUrl(url);
                            const response = await fetch(proxyUrl);
                            if (!response.ok) continue;
                            const data = await response.json();
                            return data.contents ? JSON.parse(data.contents) : data;
                        } catch (err) {
                            console.warn(`Proxy attempt failed, retrying...`);
                        }
                    }
                    await new Promise(resolve => setTimeout(resolve, backoff * (i + 1)));
                }
                throw new Error('Connectivity error');
            };

            const fetchProjects = useCallback(async () => {
                setRefreshing(true);
                setError(null);
                try {
                    const data = await fetchWithRetry('https://api.scratch.mit.edu/users/soleminer/projects');
                    if (Array.isArray(data)) {
                        setProjects(data);
                        setStats({
                            totalViews: data.reduce((acc, p) => acc + (p.stats?.views || 0), 0),
                            totalLoves: data.reduce((acc, p) => acc + (p.stats?.loves || 0), 0),
                            count: data.length
                        });
                    }
                } catch (err) {
                    setError("Scratch API connection limited. Using offline view.");
                } finally {
                    setLoading(false);
                    setRefreshing(false);
                }
            }, []);

            useEffect(() => {
                fetchProjects();
            }, [fetchProjects]);

            return (
                <div className="min-h-screen bg-slate-950 text-slate-100 selection:bg-purple-500/30">
                    <nav className="border-b border-slate-800 bg-slate-900/50 backdrop-blur-md sticky top-0 z-50">
                        <div className="max-w-7xl mx-auto px-4 h-16 flex items-center justify-between">
                            <div className="flex items-center gap-2">
                                <div className="bg-gradient-to-br from-purple-500 to-blue-500 p-2 rounded-lg shadow-lg">
                                    <Icon name="gamepad-2" className="text-white" />
                                </div>
                                <span className="text-xl font-bold tracking-tight">SOLEMINER<span className="text-purple-500 font-black italic">HUB</span></span>
                            </div>
                            <button 
                                onClick={fetchProjects}
                                disabled={refreshing}
                                className="flex items-center gap-2 px-5 py-2 rounded-full bg-slate-800 hover:bg-slate-700 border border-slate-700 transition-all disabled:opacity-50"
                            >
                                <Icon name="refresh-cw" size={16} className={refreshing ? 'animate-spin' : ''} />
                                <span className="hidden sm:inline font-semibold text-sm">{refreshing ? 'Syncing...' : 'Sync Portfolio'}</span>
                            </button>
                        </div>
                    </nav>

                    <main className="max-w-7xl mx-auto px-4 py-8">
                        {error && (
                            <div className="mb-8 p-4 bg-amber-500/10 border border-amber-500/20 rounded-2xl flex items-center justify-between gap-3 text-amber-400">
                                <div className="flex items-center gap-3">
                                    <Icon name="alert-circle" size={20} />
                                    <p className="text-sm font-medium">{error}</p>
                                </div>
                                <button onClick={fetchProjects} className="text-xs underline font-bold uppercase tracking-wider">Retry</button>
                            </div>
                        )}

                        <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-12">
                            <div className="md:col-span-2 bg-gradient-to-br from-indigo-900/40 via-purple-900/20 to-slate-900 border border-purple-500/20 rounded-[2rem] p-8 flex flex-col justify-center relative overflow-hidden group">
                                <div className="relative z-10">
                                    <div className="flex items-center gap-2 text-purple-400 font-bold text-xs uppercase tracking-[0.2em] mb-4">
                                        <Icon name="shield-check" size={14} /> Verified Developer
                                    </div>
                                    <h1 className="text-5xl font-black mb-4 text-white tracking-tighter">Creator Portfolio</h1>
                                    <p className="text-slate-400 text-lg max-w-md leading-relaxed">
                                        Showcasing the latest interactive experiences by <span className="text-white border-b-2 border-purple-500">Soleminer</span>.
                                    </p>
                                </div>
                            </div>
                            
                            <div className="bg-slate-900 border border-slate-800 rounded-[2rem] p-8 flex flex-col justify-between shadow-xl">
                                <div className="flex items-center justify-between mb-6 text-slate-500 font-bold uppercase text-[10px] tracking-[0.3em]">
                                    <span>Live Ecosystem</span>
                                    <div className="h-2 w-2 rounded-full bg-green-500 animate-pulse"></div>
                                </div>
                                <div className="space-y-6">
                                    <div>
                                        <p className="text-5xl font-black tracking-tighter">{stats.count || '0'}</p>
                                        <p className="text-slate-500 text-xs font-bold uppercase mt-1">Projects Published</p>
                                    </div>
                                    <div className="grid grid-cols-2 gap-4 pt-4 border-t border-slate-800">
                                        <div>
                                            <p className="text-xl font-bold text-purple-400">{stats.totalViews.toLocaleString()}</p>
                                            <p className="text-slate-500 text-[10px] font-bold uppercase">Plays</p>
                                        </div>
                                        <div>
                                            <p className="text-xl font-bold text-pink-500">{stats.totalLoves.toLocaleString()}</p>
                                            <p className="text-slate-500 text-[10px] font-bold uppercase">Loves</p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>

                        {selectedProject && (
                            <div className="mb-16 animate-in">
                                <div className="bg-slate-900 border-2 border-purple-500/40 rounded-[2.5rem] overflow-hidden shadow-2xl">
                                    <div className="p-5 bg-slate-800/80 flex items-center justify-between border-b border-slate-700/50">
                                        <div className="flex items-center gap-4">
                                            <Icon name="play" className="text-purple-400" />
                                            <h2 className="font-black text-xl tracking-tight">{selectedProject.title}</h2>
                                        </div>
                                        <button onClick={() => setSelectedProject(null)} className="bg-slate-700/50 hover:bg-red-500/20 px-4 py-2 rounded-xl font-bold text-sm">Close</button>
                                    </div>
                                    <div className="aspect-video bg-black">
                                        <iframe src={`https://scratch.mit.edu/projects/${selectedProject.id}/embed`} allowtransparency="true" width="100%" height="100%" frameborder="0" allowFullScreen></iframe>
                                    </div>
                                    <div className="p-8 grid grid-cols-1 md:grid-cols-2 gap-10 bg-slate-900/50">
                                        <div className="space-y-4">
                                            <h3 className="text-xs font-black text-purple-500 uppercase tracking-widest">Instructions</h3>
                                            <div className="text-slate-300 text-sm whitespace-pre-wrap">{selectedProject.instructions || "Standard Scratch controls apply."}</div>
                                        </div>
                                        <div className="flex flex-col justify-center items-end gap-6">
                                            <a href={`https://scratch.mit.edu/projects/${selectedProject.id}`} target="_blank" className="bg-purple-600 hover:bg-purple-500 text-white px-8 py-4 rounded-2xl font-black transition-all">Open in Scratch</a>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        )}

                        <div className="mb-8 flex items-center justify-between">
                            <h2 className="text-3xl font-black tracking-tighter">The Collection</h2>
                            <div className="h-px flex-1 bg-slate-800 mx-8 hidden sm:block opacity-50"></div>
                        </div>

                        {loading ? (
                            <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-8">
                                {[1, 2, 3, 4, 5, 6].map(i => (
                                    <div key={i} className="bg-slate-900 aspect-[4/3] rounded-[2rem] animate-pulse border border-slate-800/50"></div>
                                ))}
                            </div>
                        ) : (
                            <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-8">
                                {projects.map((project) => (
                                    <div key={project.id} onClick={() => { setSelectedProject(project); window.scrollTo({top: 0, behavior: 'smooth'}); }} className="group relative bg-slate-900 border border-slate-800/60 rounded-[2rem] overflow-hidden cursor-pointer hover:border-purple-500/50 transition-all duration-500 transform hover:-translate-y-2">
                                        <div className="aspect-[4/3] relative overflow-hidden bg-slate-800">
                                            <img src={project.image || (project.images && project.images['282x218'])} className="w-full h-full object-cover group-hover:scale-110 transition-all duration-700" />
                                            <div className="absolute inset-0 bg-gradient-to-t from-slate-950 via-transparent to-transparent"></div>
                                            <div className="absolute bottom-6 left-6 right-6 opacity-0 group-hover:opacity-100 transition-all">
                                                <div className="w-full py-3 bg-white text-black font-black rounded-xl text-center">START GAME</div>
                                            </div>
                                        </div>
                                        <div className="p-6">
                                            <h3 className="font-black text-xl mb-2 truncate group-hover:text-purple-400 transition-colors tracking-tight">{project.title}</h3>
                                            <div className="flex items-center justify-between text-[10px] font-bold uppercase tracking-widest text-slate-500">
                                                <span>{(project.stats?.views || 0).toLocaleString()} Views</span>
                                                <span className="text-pink-500">❤️ {(project.stats?.loves || 0).toLocaleString()}</span>
                                            </div>
                                        </div>
                                    </div>
                                ))}
                            </div>
                        )}
                    </main>
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
