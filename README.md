<div style="max-width:600px; margin:auto; font-family:Arial, sans-serif; background:#fff; border-radius:12px; box-shadow:0 4px 12px rgba(0,0,0,0.1); padding:24px; text-align:center;">

  <h2 style="color:#0078D7; margin-bottom:10px;">🛡️ Test PTI</h2>
  <p style="font-size:16px; color:#333;">
    Bonjour <b id="agentName">Agent</b> 👋  
    <br>Votre test PTI est en cours.
  </p>

  <p style="font-size:15px; color:#555; margin:20px 0;">
    Vous avez <b>15 minutes</b> ⏱️ pour valider votre test.  
  </p>

  <!-- Compte à rebours -->
  <div style="font-size:20px; font-weight:bold; color:#0078D7; margin:20px 0;">
    Temps restant : <span id="timer">15:00</span>
  </div>

  <!-- Boutons -->
  <div style="margin:25px 0;">
    <a id="btnWeb" href="#" style="display:block; background:#0078D7; color:#fff; padding:14px; margin:10px 0; border-radius:6px; font-size:16px; text-decoration:none; font-weight:bold;">
      🌐 Lancer le test (Page Web)
    </a>

    <a id="btnDirect" href="#" style="display:block; background:#28A745; color:#fff; padding:14px; margin:10px 0; border-radius:6px; font-size:16px; text-decoration:none; font-weight:bold;">
      ⚡ Confirmer directement
    </a>
  </div>

  <!-- Notice -->
  <p style="font-size:13px; color:#999; margin-top:20px;">
    ⚠️ Sans action dans les 15 minutes, le test sera considéré comme <b>échoué</b>.
  </p>
</div>

<script>
// Lire le numéro d'agent dans l'URL (pti-test?agent=467)
const urlParams = new URLSearchParams(window.location.search);
const agent = urlParams.get("agent") || "???";

// Insérer l'agent dans le texte
document.getElementById("agentName").innerText = agent;

// Adapter les liens avec le numéro d'agent
document.getElementById("btnWeb").href = "https://inasepbelgium.sharepoint.com/sites/SEU-ITT-Tlgestion/Documents%20partages/Pointage/pti-test.html?agent=" + agent;
document.getElementById("btnDirect").href = "https://tonlien.api/powerautomate?agent=" + agent;

// Compte à rebours 15 min
let time = 15 * 60; 
const timerEl = document.getElementById("timer");

function updateTimer() {
  let minutes = Math.floor(time / 60);
  let seconds = time % 60;
  timerEl.textContent = `${minutes}:${seconds.toString().padStart(2,"0")}`;
  if (time > 0) {
    time--;
    setTimeout(updateTimer, 1000);
  } else {
    timerEl.textContent = "⛔ Temps écoulé";
  }
}
updateTimer();
</script>
