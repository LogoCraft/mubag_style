import React, { useState, useEffect, useMemo, useRef } from 'react';
// Veriler, demo amaçlı olarak localStorage'da saklanır (Sadece Giriş/Kayıt için).
// Dashboard verileri Firebase Firestore'da saklanacaktır.

// Firebase Imports
import { initializeApp } from 'firebase/app';
import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from 'firebase/auth';
import { getFirestore, collection, query, onSnapshot, addDoc, serverTimestamp, deleteDoc, doc } from 'firebase/firestore'; 

// Chart.js must be loaded, let's include the script dynamically for a single-file environment
const CHART_CDN_URL = "https://cdn.jsdelivr.net/npm/chart.js@4.4.3/dist/chart.umd.min.js";

// Utility function to load Chart.js library
const loadChartJs = () => {
  return new Promise((resolve, reject) => {
    if (window.Chart) {
      resolve(window.Chart);
      return;
    }
    const script = document.createElement('script');
    script.src = CHART_CDN_URL;
    script.onload = () => resolve(window.Chart);
    script.onerror = reject;
    document.head.appendChild(script);
  });
};


// --- FIREBASE DESTEKLİ DASHBOARD BİLEŞENİ (Yeni Dashboard Kodu) ---
function FirebaseDashboard({ userEmail, logout }) {
  // State for application data and status
  const [data, setData] = useState([]);
  const [userId, setUserId] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  // New input states for structured data entry
  const [dmCount, setDmCount] = useState('');
  const [adSpend, setAdSpend] = useState('');
  const [salesCount, setSalesCount] = useState('');
  const [revenue, setRevenue] = useState('');

  // Chart refs
  const chartRef = useRef();
  const chartInstance = useRef(null); // To store the Chart.js instance

  // Firebase Instances
  const appRef = useRef(null);
  const dbRef = useRef(null);
  const authRef = useRef(null);

  // Utility to format currency (handles negative numbers)
  const formatCurrency = (amount) => {
    const isNegative = amount < 0;
    const absAmount = Math.abs(amount || 0);
    const formatted = absAmount.toLocaleString('tr-TR', { minimumFractionDigits: 2, maximumFractionDigits: 2 });
    return (isNegative ? '(-' : '') + '$' + formatted + (isNegative ? ')' : '');
  };
  
  // Utility to format count
  const formatCount = (count) => {
    return (count || 0).toLocaleString('tr-TR');
  };

  // --- 1. Firebase Initialization and Authentication ---
  useEffect(() => {
    const initializeFirebase = async () => {
      try {
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        // Check if config exists before parsing
        const firebaseConfig = typeof __firebase_config !== 'undefined' && __firebase_config ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        if (Object.keys(firebaseConfig).length === 0) {
          throw new Error("Firebase config not available. Cannot connect to database.");
        }

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);

        appRef.current = app;
        dbRef.current = db;
        authRef.current = auth;

        // Sign in using custom token or anonymously
        const signIn = async () => {
          if (initialAuthToken) {
            await signInWithCustomToken(auth, initialAuthToken);
          } else {
            await signInAnonymously(auth);
          }
        };
        await signIn();

        // Listener for auth state changes
        onAuthStateChanged(auth, (user) => {
          if (user) {
            setUserId(user.uid);
            setLoading(false);
          } else {
            setUserId(null);
            setLoading(false);
          }
        });
      } catch (e) {
        console.error("Firebase setup error:", e);
        setError(`Firebase Kurulum Hatası: ${e.message}`); // Turkish error message
        setLoading(false);
      }
    };

    initializeFirebase();
  }, []);

  // --- 2. Firestore Listener for Real-Time Data --- 
  useEffect(() => {
    if (!dbRef.current || !userId) return; // Wait for DB and UserID to be ready

    // Path matching the required security rules for private user data
    // /artifacts/{appId}/users/{userId}/dashboard_data
    const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
    const dataCollectionPath = `artifacts/${appId}/users/${userId}/dashboard_data`;
    const dataCollection = collection(dbRef.current, dataCollectionPath);

    const q = query(dataCollection);

    // Set up real-time listener (onSnapshot)
    const unsubscribe = onSnapshot(q, (snapshot) => {
      const liveData = snapshot.docs.map(doc => ({
        id: doc.id,
        ...doc.data()
      })).sort((a, b) => (a.createdAt?.seconds || 0) - (b.createdAt?.seconds || 0)); // Sort by creation time

      setData(liveData);
      console.log(`Live data updated: ${liveData.length} items`);
    }, (e) => {
        console.error("Firestore listen error:", e);
        setError(`Firestore Dinleme Hatası: ${e.message}`); // Turkish error message
    });

    // Clean up the listener when the component unmounts or dependencies change
    return () => unsubscribe();
  }, [userId]); // Re-run when userId is confirmed

  // --- 3. Structured Data Aggregation for Chart ---
  const chartData = useMemo(() => {
    const totalDm = data.reduce((sum, item) => sum + (item.dmCount || 0), 0);
    const totalAdSpend = data.reduce((sum, item) => sum + (item.adSpend || 0), 0);
    const totalSales = data.reduce((sum, item) => sum + (item.salesCount || 0), 0);
    const totalRevenue = data.reduce((sum, item) => sum + (item.revenue || 0), 0);
    // Yeni Metrik: Net Kâr (Toplam Gelir - Toplam Reklam Harcaması)
    const netProfit = totalRevenue - totalAdSpend; 

    return [
      { title: 'Toplam DM Sayısı', value: totalDm, type: 'count' },
      { title: 'Toplam Reklam Harcaması (USD)', value: totalAdSpend, type: 'currency_negative' }, 
      { title: 'Toplam Satış Adedi', value: totalSales, type: 'count' },
      { title: 'Toplam Gelir (USD)', value: totalRevenue, type: 'currency_positive' }, 
      { title: 'Toplam Net Kâr (USD)', value: netProfit, type: 'currency_net' },
    ].filter(d => d.value !== 0); // Sadece değeri sıfır olmayan metrikleri göster
  }, [data]);


  // --- 4. Chart.js Rendering Logic --- 
  useEffect(() => {
    const renderChart = async () => {
      if (!chartRef.current || chartData.length === 0) {
        if (chartInstance.current) {
          chartInstance.current.destroy();
          chartInstance.current = null;
        }
        return;
      }

      const Chart = await loadChartJs();

      if (chartInstance.current) {
        chartInstance.current.destroy();
      }
      
      // Grafik renklerini değerin negatif/pozitif olmasına göre ayarlayalım
      const backgroundColors = chartData.map(d => {
        if (d.title.includes('Kâr')) { // Net Kâr için özel renkler
          return d.value >= 0 ? 'rgba(34, 197, 94, 0.8)' : 'rgba(239, 68, 68, 0.8)'; // Yeşil/Kırmızı
        }
        if (d.title.includes('Reklam Harcaması')) {
            return 'rgba(248, 113, 113, 0.8)'; // Kırmızı
        }
        if (d.title.includes('Gelir')) {
            return d.value >= 0 ? 'rgba(52, 211, 153, 0.8)' : 'rgba(251, 191, 36, 0.8)'; // Zümrüt/Sarı
        }
        return 'rgba(6, 182, 212, 0.8)'; // Diğerleri (DM, Satış) için Teal
      });


      const ctx = chartRef.current.getContext('2d');
      chartInstance.current = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: chartData.map(d => d.title),
          datasets: [{ 
            label: 'Toplam Metrik Değeri', 
            data: chartData.map(d => d.value), 
            backgroundColor: backgroundColors,
            borderColor: 'rgb(4, 140, 165)',
            borderWidth: 1
          }]
        },
        options: { 
          responsive: true, 
          maintainAspectRatio: false,
          plugins: {
            legend: { display: false },
            tooltip: {
                callbacks: {
                    label: function(context) {
                        let label = context.dataset.label || '';
                        if (label) {
                            label += ': ';
                        }
                        const value = context.parsed.y;
                        const metricType = chartData[context.dataIndex]?.type;

                        if (metricType && metricType.startsWith('currency')) {
                            const sign = value < 0 ? '-' : '';
                            const absValue = Math.abs(value);
                            return label + sign + '$' + absValue.toLocaleString('tr-TR', { minimumFractionDigits: 2, maximumFractionDigits: 2 });
                        }
                        return label + value.toLocaleString('tr-TR');
                    }
                }
            }
          },
          scales: {
            y: {
              beginAtZero: false 
            }
          }
        }
      });
    };

    renderChart();
  }, [chartData]); // Re-run when aggregated chartData changes

  // --- 5. Data Submission Handler --- 
  const addData = async (e) => {
    e.preventDefault();
    if (!dbRef.current || !userId) {
        setError("Uygulama hazır değil. Lütfen başlatılmasını bekleyin."); 
        return;
    }

    // Convert inputs to numbers, allowing negative for adSpend and revenue
    const dm = parseInt(dmCount, 10) || 0;
    const ad = parseFloat(adSpend) || 0;
    const sales = parseInt(salesCount, 10) || 0;
    const rev = parseFloat(revenue) || 0;
    
    // YENİ KONTROL: En az bir metrik değeri sıfırdan farklı olmalı
    if (dm === 0 && ad === 0 && sales === 0 && rev === 0) {
        setError("Lütfen en az bir metrik değeri girin."); 
        return;
    }
    
    // DM ve Satış adetleri 0'dan küçük olamaz
    if (dm < 0 || sales < 0) {
        setError("DM Sayısı ve Satış Adedi negatif olamaz.");
        return;
    }
    
    setError(null);

    try {
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const dataCollectionPath = `artifacts/${appId}/users/${userId}/dashboard_data`;
        const dataCollection = collection(dbRef.current, dataCollectionPath);

        // Add the structured document to Firestore
        await addDoc(dataCollection, {
            dmCount: dm,
            adSpend: ad,
            salesCount: sales,
            revenue: rev,
            createdAt: serverTimestamp() // Firestore timestamp for ordering
        });

        // Clear the form only after successful submission
        setDmCount('');
        setAdSpend('');
        setSalesCount('');
        setRevenue('');
    } catch (e) {
        console.error("Error adding document:", e);
        setError(`Veri eklenirken hata oluştu: ${e.message}`); 
    }
  };

  // --- 5.1 Data Deletion Handler --- 
  const deleteData = async (id) => {
    if (!dbRef.current || !userId) {
        setError("Uygulama hazır değil. Lütfen bekleyin.");
        return;
    }

    try {
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const dataCollectionPath = `artifacts/${appId}/users/${userId}/dashboard_data`;
        const docRef = doc(dbRef.current, dataCollectionPath, id);
        
        await deleteDoc(docRef);
        console.log("Belge başarıyla silindi:", id);
    } catch (e) {
        console.error("Error deleting document:", e);
        setError(`Veri silinirken hata oluştu: ${e.message}`);
    }
  };
  
  const displayUserId = userEmail.split('@')[0];

  if (loading) {
    return (
      <div className="flex items-center justify-center min-h-screen bg-gray-50 p-4">
        <div className="text-xl font-semibold text-teal-600">Kontrol Paneli Yükleniyor ve Veritabanı Bağlanıyor...</div> 
      </div>
    );
  }

  // --- 6. UI Rendering --- 
  return (
    <div className="min-h-screen bg-gray-50 p-4 sm:p-8 font-inter">
        
      {/* Header */}
      <header className="flex flex-col md:flex-row justify-between items-center mb-8 border-b pb-4 bg-white p-4 rounded-xl shadow-md">
        <h1 className="text-3xl sm:text-4xl font-extrabold text-teal-700 tracking-tight">
          Instagram SaaS Metrik Takibi 
        </h1>
        <div className="flex items-center space-x-4 mt-4 md:mt-0">
            <p className="text-sm text-gray-600">
                Kullanıcı: <span className="font-mono text-xs bg-gray-200 p-1 rounded-md font-bold text-gray-700">{displayUserId}</span>
            </p>
            <button 
                onClick={logout} 
                className="px-4 py-2 bg-red-600 text-white font-semibold rounded-lg shadow-md hover:bg-red-700 transition duration-150"
            >
                Çıkış
            </button>
        </div>
      </header>


      {error && (
        <div className="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded mb-4" role="alert">
          <p className="font-bold">Hata:</p> 
          <p className="text-sm">{error}</p>
        </div>
      )}

      {/* Data Input Form - Structured Metrics */}
      <section className="bg-white p-6 rounded-xl shadow-lg mb-8">
        <h2 className="text-2xl font-semibold text-gray-800 mb-4">Günlük Metrik Girişi</h2> 
        <form onSubmit={addData} className="space-y-4">
          <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
            {/* DM Count - Adet Negatif Olamaz */}
            <div className="flex flex-col">
              <label className="text-sm font-medium text-gray-700 mb-1">Gelen DM Sayısı (Adet)</label>
              <input 
                value={dmCount}
                onChange={(e) => setDmCount(e.target.value)}
                placeholder="Örn: 45" 
                type="number" 
                min="0"
                className="border border-gray-300 p-3 rounded-lg focus:ring-2 focus:ring-teal-500 outline-none transition duration-150" 
                aria-label="Gelen DM Sayısı" 
              />
            </div>
            {/* Ad Spend - Para Negatif Olamaz (Harcama) */}
            <div className="flex flex-col">
              <label className="text-sm font-medium text-gray-700 mb-1">Reklam Harcaması (USD)</label>
              <input 
                value={adSpend}
                onChange={(e) => setAdSpend(e.target.value)}
                placeholder="Örn: 50.75" 
                type="number" 
                step="0.01"
                min="0"
                className="border border-gray-300 p-3 rounded-lg focus:ring-2 focus:ring-red-500 outline-none transition duration-150" 
                aria-label="Reklam Harcaması" 
              />
            </div>
            {/* Sales Count - Adet Negatif Olamaz */}
            <div className="flex flex-col">
              <label className="text-sm font-medium text-gray-700 mb-1">Satış Adedi (Adet)</label>
              <input 
                value={salesCount}
                onChange={(e) => setSalesCount(e.target.value)}
                placeholder="Örn: 5" 
                type="number" 
                min="0"
                className="border border-gray-300 p-3 rounded-lg focus:ring-2 focus:ring-teal-500 outline-none transition duration-150" 
                aria-label="Satış Adedi" 
              />
            </div>
            {/* Revenue - GELİR NEGATİF OLABİLİR (Zarar Durumu) */}
            <div className="flex flex-col">
              <label className="text-sm font-medium text-gray-700 mb-1">Elde Edilen Gelir (USD)</label>
              <input 
                value={revenue}
                onChange={(e) => setRevenue(e.target.value)}
                placeholder="Örn: 249.99 veya -50.00 (Zarar)" 
                type="number" 
                step="0.01"
                className="border border-gray-300 p-3 rounded-lg focus:ring-2 focus:ring-teal-500 outline-none transition duration-150" 
                aria-label="Elde Edilen Gelir" 
              />
            </div>
          </div>
          
          <button 
            type="submit" 
            className="w-full bg-teal-600 hover:bg-teal-700 text-white font-bold py-3 px-6 rounded-lg transition duration-300 shadow-md hover:shadow-lg focus:outline-none focus:ring-4 focus:ring-teal-500 focus:ring-opacity-50 mt-4"
          >
            Metrikleri Kaydet
          </button>
        </form>
      </section>

      {/* Data Visualization - Grafik */}
      <section className="bg-white p-6 rounded-xl shadow-lg mb-8">
        <h2 className="text-2xl font-semibold text-gray-800 mb-4">Toplam Metrik Görselleştirmesi (Net Kâr dahil)</h2> 
        
        {chartData.length > 0 ? (
          <div className="w-full h-[400px] max-h-screen">
            <canvas ref={chartRef} className="p-2"></canvas>
          </div>
        ) : (
          <div className="text-center py-10 text-gray-500 border-2 border-dashed border-gray-300 rounded-lg">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="mx-auto h-8 w-8 mb-3"><line x1="12" y1="20" x2="12" y2="10"></line><line x1="18" y1="20" x2="18" y2="4"></line><line x1="6" y1="20" x2="6" y2="16"></line></svg>
            <p>Henüz veri eklenmedi. Takibi başlatmak için yukarıdaki formu kullanın!</p>
          </div>
        )}
      </section>

      {/* Data Table - Tablo Görünümü */}
      <section className="bg-white p-6 rounded-xl shadow-lg">
        <h2 className="text-2xl font-semibold text-gray-800 mb-4">Detaylı Kayıtlar (Günlük Bazda)</h2>
        
        {data.length > 0 ? (
          <div className="overflow-x-auto rounded-lg border border-gray-200">
            <table className="min-w-full divide-y divide-gray-200">
              <thead className="bg-gray-50">
                <tr>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                    Kayıt Zamanı
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                    Gelen DM
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                    Reklam Harc. (USD)
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                    Satış Adedi
                  </th>
                  <th className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                    Elde Edilen Gelir (USD)
                  </th>
                  <th className="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">
                    İşlemler
                  </th>
                </tr>
              </thead>
              <tbody className="bg-white divide-y divide-gray-200">
                {data.map((item) => (
                  <tr key={item.id} className="hover:bg-gray-50 transition duration-150">
                    <td className="px-6 py-4 whitespace-nowrap text-xs text-gray-500">
                      {item.createdAt ? new Date(item.createdAt.seconds * 1000).toLocaleString('tr-TR') : 'Yükleniyor...'}
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-600 font-mono">
                      {formatCount(item.dmCount)}
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-600 font-mono">
                      {formatCurrency(item.adSpend)}
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-600 font-mono">
                      {formatCount(item.salesCount)}
                    </td>
                    <td className={`px-6 py-4 whitespace-nowrap text-sm text-gray-600 font-mono font-semibold ${item.revenue < 0 ? 'text-red-600' : 'text-emerald-600'}`}>
                      {formatCurrency(item.revenue)}
                    </td>
                    <td className="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                      <button 
                        onClick={() => deleteData(item.id)}
                        className="text-red-600 hover:text-red-800 font-semibold transition duration-150 p-1 rounded-md"
                        aria-label="Kaydı sil"
                      >
                        Sil
                      </button>
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        ) : (
          <div className="text-center py-4 text-gray-500">
            <p>Listelenecek kayıt bulunamadı.</p>
          </div>
        )}
      </section>
    </div>
  );
}


// Login Component (Değişmedi - localStorage kullanır)
function Login({ onLogin }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [isLogin, setIsLogin] = useState(true);
  const [error, setError] = useState(null);

  const toggleMode = () => {
    setIsLogin(!isLogin);
    setError(null);
    setEmail('');
    setPassword('');
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    setError(null);

    if (!email || !password) {
      setError('Email ve şifre giriniz!');
      return;
    }

    // localStorage'da kayıtlı kullanıcıları al
    const users = JSON.parse(localStorage.getItem('users')) || {};
    
    if (isLogin) {
      // Giriş işlemi
      if (users[email] && users[email] === password) {
        localStorage.setItem('current_user', email);
        onLogin(email);
      } else {
        setError('Email veya şifre yanlış! Lütfen tekrar deneyin.');
      }
    } else {
      // Kayıt işlemi
      if (users[email]) {
        setError('Bu email zaten kayıtlı! Lütfen giriş yapın.');
      } else {
        users[email] = password;
        localStorage.setItem('users', JSON.stringify(users));
        
        // Başarılı kayıttan sonra otomatik giriş yap
        localStorage.setItem('current_user', email);
        onLogin(email);
        setError('Kayıt başarılı! Otomatik olarak giriş yapıldı.');
      }
    }
  };

  return (
    <div className="flex items-center justify-center min-h-screen bg-gray-900 p-4">
      <div className="w-full max-w-md bg-gray-800 rounded-3xl p-8 shadow-2xl border border-gray-700/50">
        <h2 className="text-3xl font-bold text-center text-emerald-400 mb-8">
          {isLogin ? 'Hoş Geldiniz: Giriş Yapın' : 'Yeni Hesap Oluştur'}
        </h2>
        {error && <p className={`p-3 rounded-lg text-sm mb-4 text-center ${error.includes('başarılı') ? 'bg-green-700 text-white' : 'bg-red-700 text-white'}`}>{error}</p>}
        <form onSubmit={handleSubmit} className="space-y-6">
          <input 
            type="email" 
            placeholder="Email Adresiniz"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
            className="w-full px-5 py-3 bg-gray-700 text-gray-200 rounded-xl placeholder-gray-400 focus:outline-none focus:ring-4 focus:ring-emerald-500/50 focus:border-emerald-500 transition duration-150"
            required
          />
          <input 
            type="password" 
            placeholder="Şifreniz"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
            className="w-full px-5 py-3 bg-gray-700 text-gray-200 rounded-xl placeholder-gray-400 focus:outline-none focus:ring-4 focus:ring-emerald-500/50 focus:border-emerald-500 transition duration-150"
            required
          />
          <button 
            type="submit" 
            className="w-full py-3 mt-4 bg-emerald-600 hover:bg-emerald-700 text-white font-extrabold text-lg rounded-xl shadow-lg shadow-emerald-500/30 transition duration-200 transform hover:scale-[1.01]"
          >
            {isLogin ? 'Giriş Yap' : 'Kayıt Ol ve Başla'}
          </button>
        </form>
        <p className="text-sm text-gray-400 mt-6 text-center">
          {isLogin ? 'Hesabın yok mu?' : 'Zaten hesabın var mı?'}{' '}
          <button onClick={toggleMode} className="text-emerald-400 font-bold hover:text-emerald-300 transition duration-150 underline">
            {isLogin ? 'Kayıt Ol' : 'Giriş Yap'}
          </button>
        </p>
      </div>
    </div>
  );
}

// Main App
export default function App() {
  const [user, setUser] = useState(null);

  // Uygulama yüklendiğinde mevcut kullanıcıyı kontrol et
  useEffect(() => {
    const currentUser = localStorage.getItem('current_user');
    if (currentUser) setUser(currentUser);
  }, []);

  // Çıkış (Logout) işlevi
  const logout = () => {
    localStorage.removeItem('current_user');
    setUser(null);
  };

  // Kullanıcı oturumu açıksa Dashboard'u, yoksa Login/Register formunu göster
  return user ? <FirebaseDashboard userEmail={user} logout={logout} /> : <Login onLogin={setUser} />;
}
