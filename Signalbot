<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SignalBot Pro</title>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/dist/supabase.min.js"></script>
<script src="https://paydunya.com/assets/js/paydunya.checkout.min.js"></script>
<style>
body {
    margin:0; padding:0;
    font-family:'Poppins',sans-serif;
    display:flex; justify-content:center; align-items:center;
    min-height:100vh;
    background: linear-gradient(135deg,#0f2027,#203a43,#2c5364);
    color:#fff;
}
.container{
    background: rgba(0,0,0,0.85);
    padding:40px;
    border-radius:20px;
    box-shadow:0 20px 50px rgba(0,0,0,0.7);
    width: 400px;
    text-align:center;
    transition: all 0.5s ease;
}
input{
    width:90%; padding:14px; margin:10px 0;
    border:none; border-radius:12px; outline:none;
    background: rgba(255,255,255,0.1); color:#fff; font-size:1em;
    transition:0.3s;
}
input:focus{ background: rgba(255,255,255,0.2); }
button{
    padding:14px 25px; margin-top:15px;
    border:none; border-radius:12px;
    background:#00ffd6; color:#000; font-weight:bold; cursor:pointer;
    transition:0.3s; box-shadow: 0 5px 20px rgba(0,255,214,0.4);
}
button:hover{ background:#00d0b3; transform:translateY(-3px); box-shadow:0 8px 25px rgba(0,255,214,0.6);}
.tabs{ display:flex; justify-content:center; margin-bottom:20px; }
.tab{ flex:1; padding:12px; cursor:pointer; background: rgba(255,255,255,0.05); margin:0 5px; border-radius:12px; transition:0.3s; font-weight:bold; }
.tab.active{ background:#00ffd6; color:#000; }
.alert{ color:#ff4c4c; font-weight:bold; margin:10px 0; }
.success{ color:#4ade80; font-weight:bold; margin:10px 0; }
.hidden{ display:none; }
.card{
    background: rgba(50,50,70,0.95);
    margin:15px 0;
    padding:20px;
    border-radius:20px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.7);
    transition: transform 0.4s ease;
}
.card:hover{ transform: translateY(-5px); }
table{ width:100%; border-collapse:collapse; margin-top:15px; }
th,td{ padding:10px; text-align:center; border:1px solid rgba(255,255,255,0.2); font-size:0.9em; }
th{ background: rgba(0,255,214,0.2); }
footer{ text-align:center; margin-top:20px; font-size:0.9em; color:#aaa; }
</style>
</head>
<body>

<!-- === Auth Section === -->
<div id="auth" class="container">
    <h1>SignalBot Pro</h1>
    <div class="tabs">
        <div id="tabSignIn" class="tab active" onclick="showTab('signin')">Connexion</div>
        <div id="tabSignUp" class="tab" onclick="showTab('signup')">Inscription</div>
    </div>

    <div id="signinTab">
        <input type="email" id="emailSignIn" placeholder="Email"><br>
        <input type="password" id="passwordSignIn" placeholder="Mot de passe"><br>
        <button onclick="signIn()">Se connecter</button>
    </div>

    <div id="signupTab" class="hidden">
        <input type="email" id="emailSignUp" placeholder="Email"><br>
        <input type="password" id="passwordSignUp" placeholder="Mot de passe"><br>
        <button onclick="signUp()">S'inscrire</button>
    </div>

    <p id="authMessage" class="alert"></p>
</div>

<!-- === Platform Section (hidden until login) === -->
<div id="platform" class="hidden container">

    <h2>Bienvenue sur SignalBot Pro</h2>

    <!-- Abonnement -->
    <div id="subscription" class="card">
        <h3>Choisir un abonnement</h3>
        <select id="plan">
            <option value="2000">2000 Fr / Jour</option>
            <option value="10000">10000 Fr / Semaine</option>
            <option value="25000">25000 Fr / Mois</option>
        </select><br>
        <button onclick="pay()">Payer</button>
        <p id="paymentMessage" class="success"></p>
    </div>

    <!-- Robot Trading -->
    <div id="robot" class="card">
        <h3>Robot Trading</h3>
        <select id="asset">
            <option value="EURUSD">EUR/USD</option>
            <option value="USDJPY">USD/JPY</option>
            <option value="GBPUSD">GBP/USD</option>
            <option value="XAUUSD">XAU/USD</option>
        </select><br>
        <button onclick="generateSignal()">Générer Signal</button>
    </div>

    <!-- Historique -->
    <div id="history" class="card">
        <h3>Historique des Signaux</h3>
        <table>
            <thead>
                <tr>
                    <th>Actif</th>
                    <th>Action</th>
                    <th>Entry</th>
                    <th>Stop Loss</th>
                    <th>Take Profit</th>
                    <th>Créé le</th>
                    <th>Expiration</th>
                </tr>
            </thead>
            <tbody id="signalResult"></tbody>
        </table>
    </div>
</div>

<footer>SignalBot Pro © 2025</footer>

<script>
// === Supabase Client ===
const supabaseUrl = 'https://zywkbzqvheigruojjhrd.supabase.co';
const supabaseKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Inp5d2tienF2aGVpZ3J1bHRqIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NTcxNzk5MjMsImV4cCI6MjA3Mjc1NTkyM30.yfkj4tZ6AWeqpAWmTlFwuQkvvqOKMp799TAFapF40wY';
const supabase = supabase.createClient(supabaseUrl, supabaseKey);
let currentUser = null;

// === Tabs ===
function showTab(tab){
    if(tab==='signin'){
        document.getElementById('signinTab').classList.remove('hidden');
        document.getElementById('signupTab').classList.add('hidden');
        document.getElementById('tabSignIn').classList.add('active');
        document.getElementById('tabSignUp').classList.remove('active');
    } else {
        document.getElementById('signupTab').classList.remove('hidden');
        document.getElementById('signinTab').classList.add('hidden');
        document.getElementById('tabSignUp').classList.add('active');
        document.getElementById('tabSignIn').classList.remove('active');
    }
}

// === Inscription ===
async function signUp(){
    const email = document.getElementById('emailSignUp').value.trim();
    const password = document.getElementById('passwordSignUp').value;
    if(!email||!password){ document.getElementById('authMessage').innerText='Remplissez tous les champs.'; return;}
    const { data: existingUser } = await supabase.from('profiles').select('*').eq('id', email);
    if(existingUser.length>0){ document.getElementById('authMessage').innerText='Vous êtes déjà inscrit. Connectez-vous.'; return; }
    const { data, error } = await supabase.auth.signUp({ email, password });
    document.getElementById('authMessage').innerText = error ? error.message : 'Compte créé ! Connectez-vous.';
}

// === Connexion ===
async function signIn(){
    const email = document.getElementById('emailSignIn').value.trim();
    const password = document.getElementById('passwordSignIn').value;
    if(!email||!password){ document.getElementById('authMessage').innerText='Remplissez tous les champs.'; return;}
    const { data, error } = await supabase.auth.signInWithPassword({ email, password });
    if(error){ document.getElementById('authMessage').innerText=error.message; return;}
    currentUser = data.user;
    document.getElementById('auth').classList.add('hidden');
    document.getElementById('platform').classList.remove('hidden');
    loadSignals();
}

// === Paiement PayDunya ===
async function pay(){
    const amount = document.getElementById('plan').value;
    const email = currentUser.email;
    const reference = "txn_" + Date.now();

    PayDunya.setup({
        public_key: "live_public_EOWGkc2jnlsjlUIKCtjd220PRiD",
        token: "uGZeB2zxgxNyKsMN4rcr",
        mode: "live"
    });

    PayDunya.checkout({
        amount: parseFloat(amount),
        currency: "XOF",
        description: `Abonnement SignalBot Pro pour ${email}`,
        reference
    }, async function(response){
        if(response.status === "completed"){
            document.getElementById('paymentMessage').innerText = "Paiement validé !";
            await supabase.from('paiements').insert([{
                user_id: currentUser.id,
                amount: amount,
                currency: "XOF",
                reference: reference,
                status: 'valide',
                created_at: new Date()
            }]);
        } else {
            document.getElementById('paymentMessage').innerText = "Paiement échoué !";
        }
    });
}

// === Robot Trading ===
async function generateSignal(){
    const asset = document.getElementById('asset').value;
    const action = Math.random()>0.5?'BUY':'SELL';
    const entry = (Math.random()*100).toFixed(2);
    const stop = (action==='BUY'? entry-0.5:parseFloat(entry)+0.5).toFixed(2);
    const tp = (action==='BUY'? parseFloat(entry)+1:entry-1).toFixed(2);
    const signal = {asset_id:asset, action, entry_price:entry, stop_loss:stop, take_profit:tp, created_at:new Date(), expiration:new Date(Date.now()+24*60*60*1000)};
    await supabase.from('signals').insert([{...signal, user_id:currentUser.id}]);
    loadSignals();
}

// === Historique ===
async function loadSignals(){
    const { data, error } = await supabase.from('signals').select('*').eq('user_id', currentUser.id).order('created_at',{ascending:false});
    const tbody = document.getElementById('signalResult'); tbody.innerHTML='';
    if(error){ tbody.innerHTML='<tr><td colspan="7">'+error.message+'</td></tr>'; return;}
    if(data.length===0){ tbody.innerHTML='<tr><td colspan="7">Aucun signal pour le moment.</td></tr>'; return;}
    data.forEach(sig=>{
        const row = `<tr>
            <td>${sig.asset_id}</td>
            <td>${sig.action}</td>
            <td>${sig.entry_price}</td>
            <td>${sig.stop_loss}</td>
            <td>${sig.take_profit}</td>
            <td>${new Date(sig.created_at).toLocaleString()}</td>
            <td>${new Date(sig.expiration).toLocaleString()}</td>
        </tr>`;
        tbody.innerHTML += row;
    });
}
</script>
</body>
</html>
