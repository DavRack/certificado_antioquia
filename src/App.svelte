<script lang="ts">
  import { jsPDF } from 'jspdf';

  let nombre = $state('');
  let cedula = $state('');
  let error = $state('');

  function generarCertificado(e: Event) {
    e.preventDefault();
    if (!nombre.trim() || !cedula.trim()) {
      error = 'Por favor, completa todos los campos.';
      return;
    }
    if (!/^\d+$/.test(cedula)) {
      error = 'La cédula debe contener solo números.';
      return;
    }
    error = '';

    const doc = new jsPDF({
      orientation: 'landscape',
      unit: 'mm',
      format: 'a4'
    });

    // Draw diploma border
    doc.setLineWidth(5);
    doc.setDrawColor(0, 102, 51); // Green border for Antioquia
    doc.rect(10, 10, 277, 190);
    
    doc.setLineWidth(1);
    doc.setDrawColor(0, 0, 0);
    doc.rect(15, 15, 267, 180);

    // Title
    doc.setFont("helvetica", "bold");
    doc.setFontSize(40);
    doc.setTextColor(0, 102, 51);
    doc.text("CERTIFICADO ANTIOQUIA", 148.5, 50, { align: "center" });

    // Subtitle
    doc.setFont("helvetica", "normal");
    doc.setFontSize(20);
    doc.setTextColor(50, 50, 50);
    doc.text("Este certificado se otorga a:", 148.5, 80, { align: "center" });

    // Name
    doc.setFont("times", "bolditalic");
    doc.setFontSize(35);
    doc.setTextColor(0, 0, 0);
    doc.text(nombre.toUpperCase(), 148.5, 110, { align: "center" });

    // Document
    doc.setFont("helvetica", "normal");
    doc.setFontSize(16);
    doc.text(`Identificado(a) con cédula de ciudadanía No. ${cedula}`, 148.5, 130, { align: "center" });

    // Date
    const fecha = new Date().toLocaleDateString('es-CO', { year: 'numeric', month: 'long', day: 'numeric' });
    doc.setFontSize(14);
    doc.text(`Expedido el ${fecha}`, 148.5, 160, { align: "center" });

    // Signatures
    doc.setLineWidth(0.5);
    doc.line(70, 180, 130, 180);
    doc.setFontSize(12);
    doc.text("Gobernador", 100, 185, { align: "center" });

    doc.line(170, 180, 230, 180);
    doc.text("Secretario de Educación", 200, 185, { align: "center" });

    doc.save(`certificado_antioquia_${cedula}.pdf`);
  }
</script>

<div class="wrapper">
  <main class="card">
    <h1>Certificado Antioquia</h1>
    <p>Completa los datos para generar tu certificado oficial.</p>

    <form onsubmit={generarCertificado}>
      <div class="form-group">
        <label for="nombre">Nombre Completo</label>
        <input 
          type="text" 
          id="nombre" 
          bind:value={nombre} 
          placeholder="Ej: Juan Pérez" 
          required 
          autocomplete="name"
        />
      </div>

      <div class="form-group">
        <label for="cedula">Número de Cédula</label>
        <input 
          type="text" 
          id="cedula" 
          bind:value={cedula} 
          placeholder="Solo números" 
          inputmode="numeric"
          pattern="\d*" 
          title="Debe contener solo números" 
          required 
        />
      </div>

      {#if error}
        <p class="error">{error}</p>
      {/if}

      <button type="submit">DESCARGAR PDF</button>
    </form>
  </main>
</div>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    font-family: 'Segoe UI', Roboto, -apple-system, sans-serif;
    background-color: #e9ecef;
    color: #212529;
  }

  .wrapper {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    width: 100%;
    padding: 20px;
    box-sizing: border-box;
  }

  .card {
    background: #ffffff;
    width: 100%;
    max-width: 450px;
    padding: 2.5rem;
    border-radius: 12px;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
    box-sizing: border-box;
  }

  h1 {
    color: #006633;
    margin: 0 0 0.5rem 0;
    font-size: 1.75rem;
    text-align: center;
    font-weight: 700;
  }

  p {
    text-align: center;
    color: #6c757d;
    margin-bottom: 2rem;
    font-size: 0.95rem;
  }

  .form-group {
    margin-bottom: 1.25rem;
  }

  label {
    display: block;
    margin-bottom: 0.5rem;
    font-size: 0.9rem;
    font-weight: 600;
    color: #495057;
  }

  input {
    width: 100%;
    padding: 0.8rem 1rem;
    border: 2px solid #dee2e6;
    border-radius: 8px;
    font-size: 1rem;
    transition: border-color 0.2s, box-shadow 0.2s;
    box-sizing: border-box;
  }

  input:focus {
    outline: none;
    border-color: #006633;
    box-shadow: 0 0 0 3px rgba(0, 102, 51, 0.15);
  }

  button {
    width: 100%;
    padding: 1rem;
    background-color: #006633;
    color: #ffffff;
    border: none;
    border-radius: 8px;
    font-size: 1rem;
    font-weight: 700;
    cursor: pointer;
    transition: background-color 0.2s, transform 0.1s;
    margin-top: 1rem;
    letter-spacing: 0.5px;
  }

  button:hover {
    background-color: #004d26;
  }

  button:active {
    transform: translateY(1px);
  }

  .error {
    color: #dc3545;
    background-color: #f8d7da;
    border: 1px solid #f5c2c7;
    padding: 0.75rem;
    border-radius: 6px;
    font-size: 0.85rem;
    margin-bottom: 1rem;
    text-align: center;
  }

  /* Mobile Adjustments */
  @media (max-width: 480px) {
    .card {
      padding: 1.5rem;
    }
    
    h1 {
      font-size: 1.5rem;
    }
  }
</style>
