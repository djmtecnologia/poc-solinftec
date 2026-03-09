Como Engenheiro de QA, minha missão é garantir que cada funcionalidade, por mais simples que pareça, seja testada exaustivamente para evitar surpresas em produção. Analisei os componentes Web (React) e Mobile (React Native) fornecidos, com foco especial nas validações do formulário de "Cadastro de Equipamento Agrícola", principalmente a obrigatoriedade do campo "Chassi".

Para garantir a robustez dos testes Appium, adicionei `testID`s aos elementos do componente React Native. Esta é uma prática essencial para automação mobile, pois oferece seletores estáveis e independentes de mudanças na estrutura visual ou texto.

---

### 1. Código do Componente React Native (com `testID`s para automação)

Primeiro, aqui está o componente React Native com os `testID`s adicionados, que serão usados no script Appium.

```jsx
import React, { useState } from 'react';
import {
  View,
  Text,
  TextInput,
  TouchableOpacity,
  StyleSheet,
  Alert,
  Platform,
  ScrollView,
  KeyboardAvoidingView,
} from 'react-native';
import { Picker } from '@react-native-picker/picker'; // Importa o Picker
import DateTimePicker from '@react-native-community/datetimepicker'; // Importa o DatePicker nativo

const CadastroEquipamentoForm = () => {
  // Estados para armazenar os valores dos campos do formulário
  const [nomeEquipamento, setNomeEquipamento] = useState('');
  const [chassi, setChassi] = useState('');
  const [tipoTelemetria, setTipoTelemetria] = useState(''); // Valor inicial vazio para o picker
  const [dataAquisicao, setDataAquisicao] = useState(new Date()); // Armazena como objeto Date, inicializa com a data atual
  const [showDatePicker, setShowDatePicker] = useState(false); // Para controlar a visibilidade do date picker

  // Estados para mensagens de erro e sucesso
  const [error, setError] = useState('');
  const [successMessage, setSuccessMessage] = useState('');
  const [isLoading, setIsLoading] = useState(false); // Para controlar o estado de carregamento

  // Função para formatar a data para exibição no botão
  const formatDate = (date) => {
    return date.toLocaleDateString('pt-BR'); // Ex: 01/01/2023
  };

  // Handler para o DatePicker nativo
  const onDateChange = (event, selectedDate) => {
    const currentDate = selectedDate || dataAquisicao;
    // No iOS, o picker permanece visível até ser explicitamente fechado ou um valor ser selecionado.
    // No Android, ele fecha automaticamente após a seleção.
    setShowDatePicker(Platform.OS === 'ios' ? false : false); // Fecha o picker após a seleção
    setDataAquisicao(currentDate);
  };

  // Função para lidar com o envio do formulário
  const handleSubmit = async () => {
    setError(''); // Limpa mensagens de erro anteriores
    setSuccessMessage(''); // Limpa mensagens de sucesso anteriores
    setIsLoading(true); // Ativa o estado de carregamento

    // Validação principal: Chassi é obrigatório
    if (!chassi.trim()) {
      setError('O campo "Chassi" é obrigatório para o cadastro.');
      setIsLoading(false);
      return;
    }

    // Validação de outros campos obrigatórios
    if (!nomeEquipamento.trim() || !tipoTelemetria || !dataAquisicao) {
      setError('Por favor, preencha todos os campos obrigatórios.');
      setIsLoading(false);
      return;
    }

    // Simulação de chamada à API para cadastro
    try {
      // Em um cenário real, você faria uma requisição HTTP (ex: fetch, axios) para sua API
      // Exemplo:
      // const response = await fetch('/api/equipamentos', {
      //   method: 'POST',
      //   headers: {
      //     'Content-Type': 'application/json',
      //   },
      //   body: JSON.stringify({
      //     nomeEquipamento,
      //     chassi,
      //     tipoTelemetria,
      //     dataAquisicao: dataAquisicao.toISOString().split('T')[0], // Envia a data no formato YYYY-MM-DD
      //   }),
      // });

      // if (!response.ok) {
      //   const errorData = await response.json();
      //   throw new Error(errorData.message || 'Erro ao cadastrar equipamento.');
      // }

      // const result = await response.json(); // Se a API retornar dados do equipamento cadastrado

      // Simulação de atraso de rede para demonstrar o estado de carregamento
      await new Promise(resolve => setTimeout(resolve, 2000));

      console.log('Dados do equipamento para cadastro:', {
        nomeEquipamento,
        chassi,
        tipoTelemetria,
        dataAquisicao: dataAquisicao.toISOString().split('T')[0], // Formato YYYY-MM-DD para o backend
      });

      setSuccessMessage('Equipamento cadastrado com sucesso!');
      Alert.alert('Sucesso', 'Equipamento cadastrado com sucesso!'); // Feedback adicional via alerta nativo
      
      // Limpa o formulário após o sucesso
      setNomeEquipamento('');
      setChassi('');
      setTipoTelemetria('');
      setDataAquisicao(new Date()); // Reseta para a data atual

    } catch (err) {
      console.error('Erro no cadastro:', err);
      setError(err.message || 'Ocorreu um erro inesperado ao cadastrar o equipamento.');
      Alert.alert('Erro', err.message || 'Ocorreu um erro inesperado ao cadastrar o equipamento.');
    } finally {
      setIsLoading(false); // Desativa o estado de carregamento
    }
  };

  return (
    // KeyboardAvoidingView para ajustar a tela quando o teclado aparece
    <KeyboardAvoidingView
      style={{ flex: 1 }}
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
    >
      {/* ScrollView para permitir rolagem caso o conteúdo exceda a altura da tela */}
      <ScrollView contentContainerStyle={styles.container}>
        <Text style={styles.title} testID="formTitle">Cadastro de Equipamento Agrícola</Text>

        {/* Exibição de mensagens de erro ou sucesso */}
        {error && <Text style={styles.errorMessage} testID="errorMessageText">{error}</Text>}
        {successMessage && <Text style={styles.successMessage} testID="successMessageText">{successMessage}</Text>}

        {/* Campo: Nome do Equipamento */}
        <View style={styles.formGroup}>
          <Text style={styles.label}>Nome do Equipamento:</Text>
          <TextInput
            testID="inputNomeEquipamento"
            style={styles.input}
            value={nomeEquipamento}
            onChangeText={setNomeEquipamento}
            placeholder="Ex: Colheitadeira"
            editable={!isLoading} // Desabilita o input durante o carregamento
            autoCapitalize="words"
            returnKeyType="next"
          />
        </View>

        {/* Campo: Chassi */}
        <View style={styles.formGroup}>
          <Text style={styles.label}>Chassi:</Text>
          <TextInput
            testID="inputChassi"
            style={styles.input}
            value={chassi}
            onChangeText={setChassi}
            placeholder="Informe o número do chassi"
            editable={!isLoading} // Desabilita o input durante o carregamento
            autoCapitalize="characters"
            returnKeyType="next"
          />
        </View>

        {/* Campo: Tipo de Telemetria */}
        <View style={styles.formGroup}>
          <Text style={styles.label}>Tipo de Telemetria:</Text>
          <View style={styles.pickerContainer}>
            <Picker
              testID="pickerTipoTelemetria"
              selectedValue={tipoTelemetria}
              onValueChange={(itemValue) => setTipoTelemetria(itemValue)}
              style={styles.picker}
              enabled={!isLoading} // Desabilita o picker durante o carregamento
            >
              <Picker.Item label="Selecione o Tipo" value="" />
              <Picker.Item label="Satélite" value="SATELITE" />
              <Picker.Item label="GPRS" value="GPRS" />
            </Picker>
          </View>
        </View>

        {/* Campo: Data de Aquisição */}
        <View style={styles.formGroup}>
          <Text style={styles.label}>Data de Aquisição:</Text>
          <TouchableOpacity
            testID="buttonDataAquisicao"
            onPress={() => setShowDatePicker(true)} // Abre o date picker ao tocar
            style={styles.datePickerButton}
            disabled={isLoading} // Desabilita o botão durante o carregamento
          >
            <Text style={styles.datePickerButtonText}>
              {formatDate(dataAquisicao)} {/* Exibe a data formatada */}
            </Text>
          </TouchableOpacity>
          {showDatePicker && (
            <DateTimePicker
              testID="dateTimePicker"
              value={dataAquisicao}
              mode="date"
              // 'spinner' para iOS oferece uma experiência mais integrada. 'default' para Android.
              display={Platform.OS === 'ios' ? 'spinner' : 'default'} 
              onChange={onDateChange}
              maximumDate={new Date()} // Não permite selecionar datas futuras
            />
          )}
        </View>

        {/* Botão de Submissão */}
        <TouchableOpacity
          testID="submitButton"
          style={[styles.submitButton, isLoading && styles.submitButtonDisabled]}
          onPress={handleSubmit}
          disabled={isLoading} // Desabilita o botão durante o carregamento
        >
          <Text style={styles.submitButtonText}>
            {isLoading ? 'Cadastrando...' : 'Cadastrar Equipamento'}
          </Text>
        </TouchableOpacity>
      </ScrollView>
    </KeyboardAvoidingView>
  );
};

// Estilos utilizando StyleSheet.create para otimização e clareza
const styles = StyleSheet.create({
  container: {
    flexGrow: 1, // Permite que o ScrollView ocupe todo o espaço disponível
    padding: 20,
    backgroundColor: '#f0f2f5', // Fundo claro para a tela
  },
  title: {
    fontSize: 26,
    fontWeight: 'bold',
    textAlign: 'center',
    marginBottom: 30,
    color: '#333',
  },
  formGroup: {
    marginBottom: 20,
  },
  label: {
    fontSize: 16,
    fontWeight: '600',
    marginBottom: 8,
    color: '#555',
  },
  input: {
    backgroundColor: '#fff',
    borderWidth: 1,
    borderColor: '#ddd',
    borderRadius: 8,
    padding: 12,
    fontSize: 16,
    color: '#333',
  },
  pickerContainer: {
    backgroundColor: '#fff',
    borderWidth: 1,
    borderColor: '#ddd',
    borderRadius: 8,
    overflow: 'hidden', // Garante que o picker não vaze bordas em alguns casos
  },
  picker: {
    height: 50, // Altura padrão para o picker
    width: '100%',
    color: '#333',
  },
  datePickerButton: {
    backgroundColor: '#fff',
    borderWidth: 1,
    borderColor: '#ddd',
    borderRadius: 8,
    padding: 12,
    justifyContent: 'center',
    minHeight: 50, // Garante altura mínima para o botão
  },
  datePickerButtonText: {
    fontSize: 16,
    color: '#333',
  },
  submitButton: {
    backgroundColor: '#28a745', // Cor verde para o botão de sucesso/cadastro
    padding: 15,
    borderRadius: 8,
    alignItems: 'center',
    marginTop: 20,
  },
  submitButtonText: {
    color: '#fff',
    fontSize: 18,
    fontWeight: 'bold',
  },
  submitButtonDisabled: {
    backgroundColor: '#a0d9b5', // Cor mais clara quando desabilitado
    opacity: 0.7,
  },
  errorMessage: {
    color: '#dc3545', // Vermelho para mensagens de erro
    backgroundColor: '#f8d7da',
    borderWidth: 1,
    borderColor: '#f5c6cb',
    borderRadius: 5,
    padding: 10,
    marginBottom: 15,
    textAlign: 'center',
    fontSize: 14,
  },
  successMessage: {
    color: '#28a745', // Verde para mensagens de sucesso
    backgroundColor: '#d4edda',
    borderWidth: 1,
    borderColor: '#c3e6cb',
    borderRadius: 5,
    padding: 10,
    marginBottom: 15,
    textAlign: 'center',
    fontSize: 14,
  },
});

export default CadastroEquipamentoForm;
```

---

### 2. Script de Teste Cypress (Web)

Este script Cypress valida o formulário de cadastro de equipamento agrícola na interface Web. Ele verifica a exibição dos campos, a validação do campo "Chassi" e de outros campos obrigatórios, e o fluxo de sucesso com o estado de carregamento e a limpeza do formulário.

**Arquivo:** `cypress/e2e/cadastroEquipamento.cy.js` (ou `cypress/component/CadastroEquipamentoForm.cy.js` se for um teste de componente)

```javascript
// cypress/e2e/cadastroEquipamento.cy.js

import React from 'react';
// Ajuste o caminho conforme a estrutura do seu projeto
import CadastroEquipamentoForm from '../../src/CadastroEquipamentoForm'; 

describe('CadastroEquipamentoForm - Web', () => {
  beforeEach(() => {
    // Para testes de componente, montamos o componente diretamente.
    // Para testes E2E, você usaria cy.visit('URL_DA_SUA_APLICACAO/cadastro-equipamento');
    cy.mount(<CadastroEquipamentoForm />);
  });

  it('deve exibir o título do formulário e todos os campos de entrada', () => {
    cy.get('h2').should('contain', 'Cadastro de Equipamento Agrícola');
    cy.get('#nomeEquipamento').should('be.visible').and('have.value', '');
    cy.get('#chassi').should('be.visible').and('have.value', '');
    cy.get('#tipoTelemetria').should('be.visible').and('have.value', '');
    cy.get('#dataAquisicao').should('be.visible').and('have.value', '');
    cy.get('button[type="submit"]').should('be.visible').and('contain', 'Cadastrar Equipamento');
  });

  it('deve exibir uma mensagem de erro quando o Chassi estiver vazio na submissão', () => {
    // Preenche outros campos obrigatórios
    cy.get('#nomeEquipamento').type('Trator John Deere 7R');
    cy.get('#tipoTelemetria').select('SATELITE');
    cy.get('#dataAquisicao').type('2023-01-15'); // Formato YYYY-MM-DD para input de data

    // O Chassi é intencionalmente deixado vazio
    cy.get('#chassi').should('have.value', '');

    cy.get('button[type="submit"]').click();

    // Verifica a mensagem de erro para o Chassi
    cy.get('p[style*="errorMessage"]')
      .should('be.visible')
      .and('contain', 'O campo "Chassi" é obrigatório para o cadastro.');
    cy.get('p[style*="successMessage"]').should('not.exist'); // Garante que nenhuma mensagem de sucesso seja exibida
    cy.get('button[type="submit"]').should('not.be.disabled'); // O botão não deve estar desabilitado após a validação client-side
  });

  it('deve exibir uma mensagem de erro quando outros campos obrigatórios estiverem vazios (Chassi preenchido)', () => {
    // Preenche o Chassi
    cy.get('#chassi').type('CHASSI12345');
    // Deixa outros campos vazios

    cy.get('button[type="submit"]').click();

    // Verifica a mensagem de erro para outros campos
    cy.get('p[style*="errorMessage"]')
      .should('be.visible')
      .and('contain', 'Por favor, preencha todos os campos obrigatórios.');
    cy.get('p[style*="successMessage"]').should('not.exist');
    cy.get('button[type="submit"]').should('not.be.disabled');
  });

  it('deve cadastrar um equipamento com sucesso com todos os campos válidos', () => {
    const today = new Date();
    const year = today.getFullYear();
    const month = String(today.getMonth() + 1).padStart(2, '0');
    const day = String(today.getDate()).padStart(2, '0');
    const formattedDate = `${year}-${month}-${day}`;

    cy.get('#nomeEquipamento').type('Colheitadeira New Holland CR9090');
    cy.get('#chassi').type('NHCR9090-ABCDEF');
    cy.get('#tipoTelemetria').select('GPRS');
    cy.get('#dataAquisicao').type(formattedDate);

    cy.get('button[type="submit"]').click();

    // Verifica o estado de carregamento
    cy.get('button[type="submit"]').should('be.disabled').and('contain', 'Cadastrando...');
    cy.get('#nomeEquipamento').should('be.disabled');
    cy.get('#chassi').should('be.disabled');
    cy.get('#tipoTelemetria').should('be.disabled');
    cy.get('#dataAquisicao').should('be.disabled');

    // Espera a simulação da chamada à API ser concluída (1.5 segundos)
    cy.wait(1600); // Adiciona um pequeno buffer

    // Verifica a mensagem de sucesso
    cy.get('p[style*="successMessage"]')
      .should('be.visible')
      .and('contain', 'Equipamento cadastrado com sucesso!');
    cy.get('p[style*="errorMessage"]').should('not.exist');

    // Verifica se os campos do formulário foram limpos
    cy.get('#nomeEquipamento').should('have.value', '');
    cy.get('#chassi').should('have.value', '');
    cy.get('#tipoTelemetria').should('have.value', ''); // O valor deve ser uma string vazia após o reset
    cy.get('#dataAquisicao').should('have.value', '');

    // Verifica se o botão foi reativado e o texto revertido
    cy.get('button[type="submit"]').should('not.be.disabled').and('contain', 'Cadastrar Equipamento');
    cy.get('#nomeEquipamento').should('not.be.disabled');
    cy.get('#chassi').should('not.be.disabled');
    cy.get('#tipoTelemetria').should('not.be.disabled');
    cy.get('#dataAquisicao').should('not.be.disabled');
  });
});
```

---

### 3. Script de Teste Appium (Mobile - WebdriverIO)

Este script Appium, utilizando WebdriverIO, testa o formulário de cadastro de equipamento agrícola na aplicação Mobile. Ele interage com `TextInput`, `Picker` e `DateTimePicker`, validando a obrigatoriedade do Chassi, a submissão bem-sucedida, e o feedback visual (mensagens de erro/sucesso e alertas nativos).

**Arquivo:** `test/specs/cadastroEquipamento.e2e.js` (assumindo uma estrutura de projeto WebdriverIO)

```javascript
// test/specs/cadastroEquipamento.e2e.js

describe('Cadastro de Equipamento Agrícola - Mobile', () => {
  // Função auxiliar para obter elemento por testID (compatível com Android e iOS)
  const getElementByTestID = async (testID) => {
    // Appium mapeia testID para accessibility ID (iOS) e content-desc (Android)
    const selector = `~${testID}`; 
    return $(selector);
  };

  // Função auxiliar para fechar alertas nativos (compatível com Android e iOS)
  const dismissAlert = async () => {
    if (await driver.isAlertOpen()) {
      if (driver.isIOS) {
        await driver.acceptAlert(); // Aceita o alerta no iOS
      } else { // Android
        await driver.pressKeyCode(4); // Simula o botão "Voltar" para fechar o alerta no Android
      }
    }
  };

  beforeEach(async () => {
    // Recarrega a sessão para garantir um estado limpo antes de cada teste.
    // Em um cenário real, você pode navegar para a tela específica ou reiniciar o app.
    await driver.reloadSession(); 
    // Espera o título do formulário ser visível para garantir que o componente carregou
    await getElementByTestID('formTitle').waitForDisplayed({ timeout: 10000 });
  });

  afterEach(async () => {
    await dismissAlert(); // Fecha qualquer alerta que possa ter ficado aberto
  });

  it('deve exibir o título do formulário e todos os campos de entrada', async () => {
    await getElementByTestID('formTitle').waitForDisplayed();
    await getElementByTestID('inputNomeEquipamento').waitForDisplayed();
    await getElementByTestID('inputChassi').waitForDisplayed();
    await getElementByTestID('pickerTipoTelemetria').waitForDisplayed();
    await getElementByTestID('buttonDataAquisicao').waitForDisplayed();
    await getElementByTestID('submitButton').waitForDisplayed();

    await expect(getElementByTestID('submitButton')).toHaveText('Cadastrar Equipamento');
  });

  it('deve exibir uma mensagem de erro quando o Chassi estiver vazio na submissão', async () => {
    // Preenche outros campos obrigatórios
    await getElementByTestID('inputNomeEquipamento').setValue('Trator Massey Ferguson');

    // Seleciona 'Satélite' no Picker
    await getElementByTestID('pickerTipoTelemetria').click();
    if (driver.isIOS) {
      await $(`~Satélite`).click(); // Interação com picker no iOS
      await $('~Done').click(); // Clica em 'Done' no picker do iOS
    } else { // Interação com picker no Android
      await $(`android=new UiSelector().text("Satélite")`).click();
    }

    // Abre e seleciona uma data (ex: hoje)
    await getElementByTestID('buttonDataAquisicao').click();
    if (driver.isIOS) {
      // No iOS, o picker é um spinner, apenas o dispensamos se a data padrão (hoje) for aceitável.
      await $('~Done').click();
    } else { // No Android, o picker é um modal
      await $('~OK').click(); // Clica em OK para selecionar a data atual
    }

    // O Chassi é intencionalmente deixado vazio
    await expect(getElementByTestID('inputChassi')).toHaveText('');

    await getElementByTestID('submitButton').click();

    // Verifica a mensagem de erro na tela
    await getElementByTestID('errorMessageText').waitForDisplayed({ timeout: 5000 });
    await expect(getElementByTestID('errorMessageText')).toHaveText('O campo "Chassi" é obrigatório para o cadastro.');
    await expect(getElementByTestID('successMessageText')).not.toExist();

    // Verifica o alerta nativo de erro
    await driver.waitUntil(async () => await driver.isAlertOpen(), { timeout: 5000, timeoutMsg: 'Alerta não apareceu' });
    await expect(driver.getAlertText()).resolves.toContain('O campo "Chassi" é obrigatório para o cadastro.');
    await dismissAlert(); // Fecha o alerta

    // O botão não deve estar desabilitado após a validação client-side
    await expect(getElementByTestID('submitButton')).toBeEnabled();
  });

  it('deve exibir uma mensagem de erro quando outros campos obrigatórios estiverem vazios (Chassi preenchido)', async () => {
    // Preenche o Chassi
    await getElementByTestID('inputChassi').setValue('MF2023-XYZ');
    // Deixa outros campos vazios

    await getElementByTestID('submitButton').click();

    // Verifica a mensagem de erro na tela
    await getElementByTestID('errorMessageText').waitForDisplayed({ timeout: 5000 });
    await expect(getElementByTestID('errorMessageText')).toHaveText('Por favor, preencha todos os campos obrigatórios.');
    await expect(getElementByTestID('successMessageText')).not.toExist();

    // Verifica o alerta nativo de erro
    await driver.waitUntil(async () => await driver.isAlertOpen(), { timeout: 5000, timeoutMsg: 'Alerta não apareceu' });
    await expect(driver.getAlertText()).resolves.toContain('Por favor, preencha todos os campos obrigatórios.');
    await dismissAlert();

    await expect(getElementByTestID('submitButton')).toBeEnabled();
  });

  it('deve cadastrar um equipamento com sucesso com todos os campos válidos', async () => {
    const today = new Date();
    const formattedDate = today.toLocaleDateString('pt-BR'); // Formato esperado no botão

    await getElementByTestID('inputNomeEquipamento').setValue('Plantadeira Tatu Marchesan');
    await getElementByTestID('inputChassi').setValue('TATU-PLANT-12345');

    // Seleciona 'GPRS' no Picker
    await getElementByTestID('pickerTipoTelemetria').click();
    if (driver.isIOS) {
      await $(`~GPRS`).click();
      await $('~Done').click();
    } else {
      await $(`android=new UiSelector().text("GPRS")`).click();
    }

    // Abre e seleciona uma data (ex: hoje)
    await getElementByTestID('buttonDataAquisicao').click();
    if (driver.isIOS) {
      // Para iOS, se quisermos selecionar uma data específica, interagiríamos com os spinners.
      // Para simplificar, apenas dispensamos, assumindo que a data padrão (hoje) está ok.
      await $('~Done').click();
    } else { // Android
      // Para selecionar uma data específica no Android, interagiríamos com a UI do date picker.
      // Para este teste, apenas aceitamos a data padrão (hoje).
      await $('~OK').click();
    }
    await expect(getElementByTestID('buttonDataAquisicao')).toHaveText(formattedDate);

    await getElementByTestID('submitButton').click();

    // Verifica o estado de carregamento
    await expect(getElementByTestID('submitButton')).toHaveText('Cadastrando...');
    await expect(getElementByTestID('submitButton')).toBeDisabled();
    await expect(getElementByTestID('inputNomeEquipamento')).toBeDisabled();
    await expect(getElementByTestID('inputChassi')).toBeDisabled();
    // O Picker e o botão do DatePicker também ficam desabilitados, mas verificar os TextInputs é suficiente.

    // Espera a simulação da chamada à API ser concluída (2 segundos)
    await driver.pause(2100); // Adiciona um pequeno buffer

    // Verifica a mensagem de sucesso na tela
    await getElementByTestID('successMessageText').waitForDisplayed({ timeout: 5000 });
    await expect(getElementByTestID('successMessageText')).toHaveText('Equipamento cadastrado com sucesso!');
    await expect(getElementByTestID('errorMessageText')).not.toExist();

    // Verifica o alerta nativo de sucesso
    await driver.waitUntil(async () => await driver.isAlertOpen(), { timeout: 5000, timeoutMsg: 'Alerta não apareceu' });
    await expect(driver.getAlertText()).resolves.toContain('Equipamento cadastrado com sucesso!');
    await dismissAlert();

    // Verifica se os campos do formulário foram limpos
    await expect(getElementByTestID('inputNomeEquipamento')).toHaveText('');
    await expect(getElementByTestID('inputChassi')).toHaveText('');
    // O valor do Picker reseta para o padrão, a Data reseta para hoje.
    await expect(getElementByTestID('buttonDataAquisicao')).toHaveText(new Date().toLocaleDateString('pt-BR'));

    // Verifica se o botão foi reativado e o texto revertido
    await expect(getElementByTestID('submitButton')).toBeEnabled();
    await expect(getElementByTestID('submitButton')).toHaveText('Cadastrar Equipamento');
    await expect(getElementByTestID('inputNomeEquipamento')).toBeEnabled();
    await expect(getElementByTestID('inputChassi')).toBeEnabled();
  });
});
```