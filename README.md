<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Coffre 3D & Lettre</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: radial-gradient(circle at top, #222 0%, #050509 60%);
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      color: #fff;
    }

    .container {
      text-align: center;
      perspective: 1000px;
    }

    .coffre-wrapper {
      width: 220px;
      height: 160px;
      margin: 0 auto;
      position: relative;
      cursor: pointer;
      transform-style: preserve-3d;
    }

    .coffre-base {
      position: absolute;
      bottom: 0;
      left: 50%;
      transform: translateX(-50%);
      width: 200px;
      height: 90px;
      background: linear-gradient(135deg, #6b3a17, #3b1f0a);
      border-radius: 10px;
      border: 3px solid #d9993b;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.7);
    }

    .coffre-lid {
      position: absolute;
      bottom: 90px;
      left: 50%;
      transform: translateX(-50%) rotateX(0deg);
      transform-origin: bottom center;
      width: 200px;
      height: 70px;
      background: linear-gradient(135deg, #7f4b20, #4a290e);
      border-radius: 14px 14px 6px 6px;
      border: 3px solid #d9993b;
      box-shadow: 0 8px 15px rgba(0, 0, 0, 0.8);
      transition: transform 0.6s ease;
    }

    .coffre-base::before {
      content: "";
      position: absolute;
      inset: 0;
      border-top: 3px solid #e4c26b;
      opacity: 0.7;
    }

    .coffre-lid::before {
      content: "";
      position: absolute;
      inset: 0;
      border-bottom: 3px solid #e4c26b;
      opacity: 0.7;
    }

    .coffre-lock {
      position: absolute;
      bottom: 30px;
      left: 50%;
      transform: translateX(-50%);
      width: 36px;
      height: 46px;
      background: radial-gradient(circle at top, #ffe9a0, #d6a23a);
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.7);
      z-index: 2;
    }

    .coffre-lock::before {
      content: "";
      position: absolute;
      top: -18px;
      left: 50%;
      transform: translateX(-50%);
      width: 24px;
      height: 24px;
      border-radius: 40px 40px 20px 20px;
      border: 4px solid #f0cf70;
      border-bottom: 4px solid transparent;
    }

    .coffre-lock::after {
      content: "";
      position: absolute;
      top: 18px;
      left: 50%;
      transform: translateX(-50%);
      width: 6px;
      height: 12px;
      border-radius: 6px;
      background: #8b5a1a;
    }

    /* Effet ouverture */
    body.open .coffre-lid {
      transform: translateX(-50%) rotateX(-85deg);
    }

    /* Lettre */
    #lettre {
      margin-top: 40px;
      width: 260px;
      height: 150px;
      background: #fdf7e2;
      border-radius: 8px;
      padding: 18px;
      color: #333;
      display: none;
      margin-left: auto;
      margin-right: auto;
      box-shadow: 0 0 25px rgba(255, 255, 255, 0.35);
      transform-origin: top center;
      transform: scaleY(0.7) translateY(20px);
      opacity: 0;
      transition: opacity 0.5s ease, transform 0.5s ease;
    }

    body.open #lettre {
      display: block;
      opacity: 1;
      transform: scaleY(1) translateY(0);
    }

    #lettre h2 {
      margin-bottom: 10px;
      font-size: 1.1rem;
    }

    #lettre p {
      font-size: 0.95rem;
    }

    .hint {
      margin-top: 10px;
      font-size: 0.9rem;
      opacity: 0.8;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="coffre-wrapper" id="coffre">
      <div class="coffre-base"></div>
      <div class="coffre-lid"></div>
      <div class="coffre-lock"></div>
    </div>
    <div class="hint">Clique sur le coffre pour ouvrir la lettre</div>

    <div id="lettre">
      <h2>Message secret</h2>
      <p>Antohny est gay</p>
    </div>
  </div>

  <script>
    const coffre = document.getElementById('coffre');

    coffre.addEventListener('click', () => {
      document.body.classList.add('open');
    });
  </script>
</body>
</html>
