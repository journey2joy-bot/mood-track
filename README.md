<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Simple Mood Tracker</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/color-thief/2.3.2/color-thief.umd.js"></script>
  <style>
    /* General Styles */
    body {
      font-family: 'Arial', sans-serif;
      background-color: #f4f6f8;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      color: #333;
    }

    h1 {
      font-size: 2.5em;
      color: #5c6bc0;
      margin-bottom: 20px;
      text-align: center;
    }

    .result {
      margin-top: 20px;
      background-color: #fff;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
      width: 80%;
      max-width: 600px;
      text-align: center;
    }

    #preview {
      max-width: 100%;
      border-radius: 10px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }

    input[type="file"] {
      background-color: #5c6bc0;
      color: white;
      padding: 10px 20px;
      font-size: 1.2em;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }

    input[type="file"]:hover {
      background-color: #3f51b5;
    }

    /* Responsive Design */
    @media screen and (max-width: 768px) {
      h1 {
        font-size: 2em;
      }

      .result {
        width: 90%;
      }
    }

    /* Animations */
    .fade-in {
      animation: fadeIn 1.5s ease-in-out;
    }

    @keyframes fadeIn {
      from {
        opacity: 0;
      }
      to {
        opacity: 1;
      }
    }

    /* Result Styling */
    .result p {
      font-size: 1.2em;
      line-height: 1.5;
    }

    .result h2 {
      color: #5c6bc0;
    }

    .result strong {
      color: #2196f3;
    }
  </style>
</head>
<body>

<h1>Mood Tracker</h1>

<input type="file" id="upload" accept="image/*">
<br>
<img id="preview" class="fade-in" crossorigin="anonymous" />
<div class="result fade-in" id="result"></div>

<script>
  const upload = document.getElementById('upload');
  const preview = document.getElementById('preview');
  const result = document.getElementById('result');
  const colorThief = new ColorThief();

  upload.addEventListener('change', function() {
    const file = upload.files[0];
    const reader = new FileReader();
    reader.onload = function(e) {
      preview.src = e.target.result;
    }
    reader.readAsDataURL(file);
  });

  preview.addEventListener('load', function() {
    const colors = colorThief.getPalette(preview, 5); // Ambil 5 warna dominan
    let mood = '';
    let colorCounts = {
      'Marah': 0,
      'Bimbang': 0,
      'Biasa': 0,
      'Sedih': 0,
      'Bahagia': 0
    };

    colors.forEach(color => {
      const [r, g, b] = color;

      // Marah = Merah
      if (r > 150 && g < 100 && b < 100) {
        colorCounts['Marah']++;
      }
      // Bimbang = Ungu
      else if (r > 100 && b > 100 && r > g && b > g) {
        colorCounts['Bimbang']++;
      }
      // Biasa = Hitam
      else if (r < 50 && g < 50 && b < 50) {
        colorCounts['Biasa']++;
      }
      // Sedih = Biru
      else if (r < 50 && g < 100 && b > 150) {
        colorCounts['Sedih']++;
      }
      // Bahagia = Hijau
      else if (r < 100 && g > 150 && b < 50) {
        colorCounts['Bahagia']++;
      }
    });

    // Tentukan mood berdasarkan warna dominan terbanyak
    const maxMood = Object.keys(colorCounts).reduce((a, b) => colorCounts[a] > colorCounts[b] ? a : b);

    // Pilihan saran untuk setiap mood
    const suggestions = {
      'Bimbang': [
        'Cobalah untuk meluangkan waktu sejenak untuk merefleksikan perasaan dan situasi yang membuat bingung.',
        'Berbicara dengan seseorang yang dipercaya bisa membantu menenangkan pikiran.',
        'Tanyakan pada diri sendiri apa yang membuat Anda bingung dan coba untuk menghadapinya.',
        'Melakukan meditasi atau beristirahat sejenak bisa memberi perspektif baru.',
        'Jangan ragu untuk mencari bantuan jika merasa bingung.'
      ],
      'Bahagia': [
        'Nikmati momen kebahagiaan ini, dan berbagi kebahagiaan dengan orang lain.',
        'Teruskan kebiasaan positif dan berterima kasih atas hal-hal baik dalam hidup.',
        'Lakukan kegiatan yang membuat Anda bahagia lebih sering.',
        'Bergabung dengan teman-teman untuk berbagi kebahagiaan.',
        'Bersyukur setiap hari dapat memperkuat rasa bahagia.'
      ],
      'Marah': [
        'Cobalah teknik relaksasi untuk menenangkan diri.',
        'Ambil waktu sejenak untuk bernapas dan meredakan ketegangan.',
        'Berbicara dengan orang yang Anda percayai untuk mengurangi rasa marah.',
        'Olahraga ringan bisa membantu melepaskan ketegangan akibat marah.',
        'Jangan ragu untuk mencari dukungan profesional jika perlu.'
      ],
      'Biasa': [
        'Cobalah untuk menemukan kegiatan baru yang bisa memberi kebahagiaan.',
        'Berbicara dengan orang terdekat bisa memberi perspektif baru.',
        'Waktu untuk diri sendiri bisa membantu Anda menemukan keseimbangan.',
        'Cobalah untuk lebih aktif dan mencoba hal baru.',
        'Kadang, perasaan biasa memberi ruang untuk merencanakan langkah selanjutnya.'
      ],
      'Sedih': [
        'Berbicara dengan orang terdekat atau seorang profesional bisa membantu.',
        'Cobalah untuk beristirahat dan memberi ruang bagi perasaan Anda.',
        'Lakukan aktivitas yang memberi ketenangan, seperti mendengarkan musik.',
        'Menulis tentang perasaan Anda bisa membantu melepaskannya.',
        'Jangan ragu untuk mencari dukungan jika perasaan sedih berlangsung lama.'
      ]
    };

    // Pilih saran acak
    const randomSuggestion = suggestions[maxMood][Math.floor(Math.random() * suggestions[maxMood].length)];

    // Hasil analisis
    result.innerHTML = `
      <h2>Hasil Analisis Mood</h2>
      <p>Warna Dominan: rgb(${colors[0].join(', ')})</p>
      <p>Perkiraan Mood: <strong>${maxMood}</strong></p>
      <h3>Saran untuk Anda:</h3>
      <p>${randomSuggestion}</p>
    `;
  });
</script>

</body>
</html>
