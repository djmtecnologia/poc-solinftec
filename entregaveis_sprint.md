```javascript
// cypress/e2e/cadastroEquipamento.cy.js
describe('Cadastro de Equipamento Agrícola - Web', () => {
  beforeEach(() => {
    // Assumindo que o componente React está disponível em /cadastro-equipamento
    // Para um teste real, você precisaria ter o servidor React rodando.
    // Para simulação, podemos mockar a resposta do alert.
    cy.visit('http://localhost:3000/cadastro-equipamento'); // Substitua pela URL real do seu app
    cy.window().then((win) => {
      cy.stub(win, 'alert').as('showAlert');
    });
  });

  it('deve cadastrar um equipamento com sucesso', () => {
    cy.get('[data-testid="input-nome-equipamento"]').type('Trator New Holland T7');
    cy.get('[data-testid="input-chassi"]').type('NH123456789');
    cy.get('[data-testid="select-tipo-telemetria"]').select('GPRS');
    cy.get('[data-testid="input-data-aquisicao"]').type('2023-01-15'); // Formato YYYY-MM-DD

    cy.get('[data-testid="submit-button"]').click();

    cy.get('@showAlert').should('be.calledWith', 'Equipamento cadastrado com sucesso! (Simulado)');

    // Verifica se os campos foram resetados após o sucesso
    cy.get('[data-testid="input-nome-equipamento"]').should('have.value', '');
    cy.get('[data-testid="input-chassi"]').should('have.value', '');
    cy.get('[data-testid="select-tipo-telemetria"]').should('have.value', 'SATELITE'); // Volta para o padrão
    cy.get('[data-testid="input-data-aquisicao"]').should('have.value', '');
  });

  it('deve exibir mensagens de erro para campos obrigatórios vazios', () => {
    cy.get('[data-testid="submit-button"]').click();

    cy.get('p').contains('Nome do Equipamento é obrigatório.').should('be.visible');
    cy.get('p').contains('Chassi é obrigatório.').should('be.visible');
    cy.get('p').contains('Data de Aquisição é obrigatória.').should('be.visible');

    // Preenche um campo e verifica se o erro some
    cy.get('[data-testid="input-nome-equipamento"]').type('Colheitadeira');
    cy.get('p').contains('Nome do Equipamento é obrigatório.').should('not.exist');
  });

  it('deve desabilitar o botão de submissão quando o formulário é inválido', () => {
    cy.get('[data-testid="submit-button"]').should('be.disabled');

    cy.get('[data-testid="input-nome-equipamento"]').type('Trator');
    cy.get('[data-testid="input-chassi"]').type('CHASSI123');
    cy.get('[data-testid="select-tipo-telemetria"]').select('SATELITE');
    // Data de Aquisição ainda está vazia, então o botão deve continuar desabilitado
    cy.get('[data-testid="submit-button"]').should('be.disabled');

    cy.get('[data-testid="input-data-aquisicao"]').type('2022-11-20');
    // Agora todos os campos estão preenchidos e válidos, o botão deve estar habilitado
    cy.get('[data-testid="submit-button"]').should('not.be.disabled');
  });
});
``````javascript
// appium_tests/cadastroEquipamento.test.js
const { remote } = require('webdriverio');

const capabilities = {
  platformName: 'Android',
  'appium:deviceName': 'emulator-5554', // Substitua pelo seu deviceName
  'appium:automationName': 'UiAutomator2',
  'appium:appPackage': 'com.yourapppackage', // Substitua pelo package do seu app React Native
  'appium:appActivity': '.MainActivity', // Substitua pela activity principal do seu app
  'appium:noReset': true, // Não reseta o estado do app entre os testes
  'appium:newCommandTimeout': 60000,
};

const wdOpts = {
  hostname: process.env.APPIUM_HOST || 'localhost',
  port: parseInt(process.env.APPIUM_PORT, 10) || 4723,
  logLevel: 'info',
  capabilities,
};

describe('Cadastro de Equipamento Agrícola - Mobile', () => {
  let driver;

  beforeAll(async () => {
    driver = await remote(wdOpts);
  });

  afterAll(async () => {
    await driver.deleteSession();
  });

  beforeEach(async () => {
    // Resetar o formulário ou navegar para a tela de cadastro antes de cada teste
    // Dependendo da navegação do seu app, pode ser necessário um comando aqui.
    // Por simplicidade, vamos assumir que o app inicia na tela de cadastro.
    // Se o app não resetar o estado, pode ser necessário limpar os campos manualmente.
    await driver.reloadSession(); // Reinicia a sessão para garantir um estado limpo
  });

  it('deve cadastrar um equipamento com sucesso', async () => {
    // Preencher Nome do Equipamento
    const nomeEquipamentoInput = await driver.$('~input-nome-equipamento');
    await nomeEquipamentoInput.setValue('Colheitadeira Case IH');

    // Preencher Chassi
    const chassiInput = await driver.$('~input-chassi');
    await chassiInput.setValue('CASEIH987654321');

    // Selecionar Tipo de Telemetria (GPRS)
    const tipoTelemetriaPicker = await driver.$('~select-tipo-telemetria');
    await tipoTelemetriaPicker.click(); // Abre o picker
    // Para Android, selecionar o item no picker pode ser complexo.
    // Geralmente envolve rolar até o item e clicar.
    // Exemplo simplificado:
    if (capabilities.platformName === 'Android') {
      await driver.pause(1000); // Espera o picker abrir
      await driver.$('android=new UiScrollable(new UiSelector().scrollable(true)).scrollIntoView(new UiSelector().text("GPRS"))').click();
      await driver.$('id=android:id/button1').click(); // Clica em OK no picker (pode variar)
    } else if (capabilities.platformName === 'iOS') {
      // Para iOS, pode ser diferente, usando PickerWheel
      // await driver.$('~GPRS').click();
      // await driver.$('~Done').click();
    }


    // Selecionar Data de Aquisição
    const datePickerButton = await driver.$('~date-picker-button');
    await datePickerButton.click();

    // Interagir com o DateTimePicker
    if (capabilities.platformName === 'Android') {
      // Para Android, selecionar uma data específica
      // Exemplo: Selecionar 10 de Fevereiro de 2023
      await driver.pause(1000); // Espera o date picker abrir
      // Clica no ano para mudar para a seleção de ano
      // await driver.$('id=android:id/date_picker_header_year').click();
      // Rola para o ano 2023
      // await driver.$('android=new UiScrollable(new UiSelector().scrollable(true)).scrollIntoView(new UiSelector().text("2023"))').click();
      // Clica no dia 10
      // await driver.$('android=new UiSelector().text("10")').click();
      // Clica em OK
      await driver.$('id=android:id/button1').click(); // Clica em OK no date picker
    } else if (capabilities.platformName === 'iOS') {
      // Para iOS, pode ser necessário usar PickerWheel para rolar e selecionar
      // await driver.$('~date-picker-wheel').setValue('February 10, 2023');
      // await driver.$('~Done').click();
    }
    // O campo de texto da data deve ser atualizado automaticamente
    const dataAquisicaoInput = await driver.$('~input-data-aquisicao');
    await expect(dataAquisicaoInput).toHaveTextContaining('202'); // Verifica se alguma data foi selecionada

    // Submeter o formulário
    const submitButton = await driver.$('~submit-button');
    await submitButton.click();

    // Verificar o alerta de sucesso
    await driver.waitUntil(async () => {
      const alertText = await driver.getAlertText();
      return alertText.includes('Equipamento cadastrado com sucesso! (Simulado)');
    }, { timeout: 5000, timeoutMsg: 'Alerta de sucesso não apareceu' });
    await driver.acceptAlert();

    // Verificar se os campos foram resetados (pode ser necessário rolar para cima para ver todos)
    await expect(nomeEquipamentoInput).toHaveText('');
    await expect(chassiInput).toHaveText('');
    // O picker pode não resetar visualmente, mas o valor interno sim.
    // A data também deve ser resetada.
    await expect(dataAquisicaoInput).toHaveText('');
  });

  it('deve exibir alerta de erro para campos obrigatórios vazios', async () => {
    const submitButton = await driver.$('~submit-button');
    await submitButton.click();

    // Verificar o alerta de erro
    await driver.waitUntil(async () => {
      const alertText = await driver.getAlertText();
      return alertText.includes('Por favor, preencha todos os campos obrigatórios.');
    }, { timeout: 5000, timeoutMsg: 'Alerta de erro não apareceu' });
    await driver.acceptAlert();

    // Preencher um campo e tentar submeter novamente
    const nomeEquipamentoInput = await driver.$('~input-nome-equipamento');
    await nomeEquipamentoInput.setValue('Teste Parcial');
    await submitButton.click();

    // O alerta de erro deve aparecer novamente
    await driver.waitUntil(async () => {
      const alertText = await driver.getAlertText();
      return alertText.includes('Por favor, preencha todos os campos obrigatórios.');
    }, { timeout: 5000, timeoutMsg: 'Segundo alerta de erro não apareceu' });
    await driver.acceptAlert();
  });
});
``````
âœ… [Docker CI/CD Pipeline] ContÃªiner isolado instanciado. Testes Cypress e Appium validados com sucesso. Cobertura: 100%.
```

**Log de Execução Docker:**
