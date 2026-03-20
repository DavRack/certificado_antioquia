<script lang="ts">
  import { jsPDF } from 'jspdf';

  let nombre = $state('');
  let cedula = $state('');
  let fechaNacimiento = $state('');
  let lugarNacimiento = $state('');
  let error = $state('');

  function generarCertificado(e: Event) {
    e.preventDefault();
    if (!nombre.trim() || !cedula.trim() || !fechaNacimiento.trim() || !lugarNacimiento.trim()) {
      error = 'Por favor, completa todos los campos.';
      return;
    }
    if (!/^\d+$/.test(cedula)) {
      error = 'La cédula debe contener solo números.';
      return;
    }
    error = '';

    // "letter" (carta) is 215.9 x 279.4 mm
    const doc = new jsPDF({
      orientation: 'portrait',
      unit: 'mm',
      format: 'letter'
    });

    const pageWidth = doc.internal.pageSize.getWidth();
    const pageHeight = doc.internal.pageSize.getHeight();

    // Draw diploma border
    doc.setLineWidth(5);
    doc.setDrawColor(0, 102, 51); // Green border for Antioquia
    doc.rect(10, 10, pageWidth - 20, pageHeight - 20);
    
    doc.setLineWidth(1);
    doc.setDrawColor(0, 0, 0);
    doc.rect(15, 15, pageWidth - 30, pageHeight - 30);

    // Title
    doc.setFont("helvetica", "bold");
    doc.setFontSize(36);
    doc.setTextColor(0, 102, 51);
    doc.text("ORGULLOSAMENTE ANTIOQUEÑO", pageWidth / 2, 60, { align: "center" });

    // Subtitle
    doc.setFont("helvetica", "normal");
    doc.setFontSize(20);
    doc.setTextColor(50, 50, 50);
    doc.text("Este certificado se otorga a:", pageWidth / 2, 90, { align: "center" });

    // Name
    doc.setFont("times", "bolditalic");
    doc.setFontSize(32);
    doc.setTextColor(0, 0, 0);
    doc.text(nombre.toUpperCase(), pageWidth / 2, 120, { align: "center" });

    // Document
    doc.setFont("helvetica", "normal");
    doc.setFontSize(16);
    doc.text(`Identificado(a) con cédula de ciudadanía No. ${cedula}`, pageWidth / 2, 140, { align: "center" });

    // Birth info
    doc.setFontSize(14);
    doc.text(`Nacido(a) el ${fechaNacimiento} en ${lugarNacimiento}`, pageWidth / 2, 160, { align: "center" });

    // Date
    const fechaActual = new Date().toLocaleDateString('es-CO', { year: 'numeric', month: 'long', day: 'numeric' });
    doc.setFontSize(14);
    doc.text(`Expedido el ${fechaActual}`, pageWidth / 2, 190, { align: "center" });

    // Signatures
    doc.setLineWidth(0.5);
    doc.line(pageWidth / 2 - 50, 240, pageWidth / 2 - 10, 240);
    doc.setFontSize(12);
    doc.text("Gobernador", pageWidth / 2 - 30, 245, { align: "center" });

    doc.line(pageWidth / 2 + 10, 240, pageWidth / 2 + 50, 240);
    doc.text("Secretario de Educación", pageWidth / 2 + 30, 245, { align: "center" });

    doc.save(`certificado_antioquia_${cedula}.pdf`);
  }
</script>

<div class="wrapper">
  <main class="card">
    <h1>Orgullosamente antioqueño</h1>
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

      <div class="form-group-row">
        <div class="form-group">
          <label for="fechaNacimiento">Fecha de Nacimiento</label>
          <input 
            type="date" 
            id="fechaNacimiento" 
            bind:value={fechaNacimiento} 
            required 
          />
        </div>

        <div class="form-group">
          <label for="lugarNacimiento">Lugar de Nacimiento</label>
          <input 
            type="text" 
            id="lugarNacimiento" 
            bind:value={lugarNacimiento} 
            placeholder="Ej: Medellín" 
            required 
          />
        </div>
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
    max-width: 500px;
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
    width: 100%;
  }

  .form-group-row {
    display: flex;
    gap: 1rem;
    width: 100%;
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

  input[type="date"] {
    font-family: inherit;
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
    
    .form-group-row {
      flex-direction: column;
      gap: 0;
    }
  }
</style>
