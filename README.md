<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Test PTI</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f9fafb;
      color: #333;
      text-align: center;
      padding: 40px;
    }
    .card {
      background: white;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      max-width: 600px;
      margin: auto;
    }
    h1 {
      color: #0078d7;
    }
    .success { color: green; font-weight: bold; }
    .error { color: red; font-weight: bold; }
    .info {
      margin-top: 20px;
      text-align: left;
      background: #f0f8ff;
      border-left: 5px solid #0078d7;
      padding: 15px;
      border-radius: 6px;
    }
    .timer {
      font-size: 20px;
      font-weight: bold;
      margin-top: 10px;
      color: #0078d7;
    }
    .btn {
      display: inline-block;
      background: #0078d7;
      color: #fff;
      padding: 14px 28px;
      margin-top: 20px;
      border-radius: 8px;
      font-size: 16px;
      text-decoration: none;
      font-weight: bold;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div class="card">
    <h1>🚨 Test PTI</h1>
    <div id="pti-message"></div>
    <div class="timer" id="pti-timer"></div>
    <button id="manualBtn" class="btn" style="display:none;">▶️ Confirmer le test</button>
    <div class="info">
      <h3>Que faire ?</h3>
      <ol>
        <li>Vérifiez que votre module PTI est bien connecté.</li>
        <li>Appuyez sur le bouton d’alerte dans les <b>15 minutes</b>.</li>
        <li>Si nécessaire, cliquez aussi sur <b>Confirmer le test</b>.</li>
        <li>Vous recevrez ensuite un e-mail : <i>“Test réussi”</i> ou <i>“Test échoué”</i>.</li>
      </ol>
    </div>
  </div>

  <script>
    // ⚠️ Mets ici l’URL publique de ton deuxième flux (HTTP Request trigger)
    const FLOW_URL = "https://defaulted2a90e4d79b4b2283fb7ac22cf5bd.d0.environment.api.powerplatform.com:443/powerautomate/automations/direct/workflows/6f563304072643d38be2f69ee6731a6b/triggers/manual/paths/invoke?api-version=1&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=Qpgn-Bk9vi1dOnMuB3o-LJbUB_UVPl6p-CfUgO1wLns";

    function getAgentCode() {
      const url = new URL(window.location.href);
      return url.searchParams.get("agent");
    }

    const agent = getAgentCode();
    const messageDiv = document.getElementById("pti-message");
    const timerDiv = document.getElementById("pti-timer");
    const manualBtn = document.getElementById("manualBtn");

    if (!agent) {
      messageDiv.innerHTML = "<p class='error'>❌ Code agent manquant dans l’URL.<br>Veuillez cliquer de nouveau depuis l’e-mail.</p>";
    } else {
      messageDiv.innerHTML = "<p class='success'>✅ Test en cours pour l’agent <b>" + agent + "</b>.</p>";

      // Timer 15 minutes
      let timeLeft = 15 * 60;
      const timer = setInterval(() => {
        let min = Math.floor(timeLeft / 60);
        let sec = timeLeft % 60;
        timerDiv.innerText = "⏳ Temps restant : " + min + "m " + (sec < 10 ? "0" : "") + sec + "s";
        timeLeft--;

        if (timeLeft < 0) {
          clearInterval(timer);
          timerDiv.innerText = "⏰ Temps écoulé !";
        }
      }, 1000);

      // Fonction d’envoi vers Power Automate
      function sendToFlow() {
        fetch(FLOW_URL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ agent: agent })
        })
        .then(response => {
          if (!response.ok) throw new Error("Erreur réseau " + response.status);
          return response.text();
        })
        .then(data => {
          console.log("Flux déclenché :", data);
          messageDiv.innerHTML += "<p class='success'>✅ Signal envoyé vers Power Automate.</p>";
        })
        .catch(error => {
          console.error("Erreur :", error);
          messageDiv.innerHTML += "<p class='error'>⚠️ Erreur lors de l’envoi automatique.<br>Vous pouvez cliquer sur <b>Confirmer le test</b>.</p>";
          manualBtn.style.display = "inline-block";
        });
      }

      // Envoi automatique au chargement
      sendToFlow();

      // Bouton manuel
      manualBtn.addEventListener("click", sendToFlow);
    }
  </script>
</body>
</html>
