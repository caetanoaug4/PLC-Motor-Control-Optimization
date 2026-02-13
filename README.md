# PLC-Motor-Control-Optimization

// ==============================================================================
// 1. EDGE DETECTION | DETECÇÃO DE BORDA
// EN: Detects the rising edge of the toggle button to prevent multiple triggers.
// PT: Detecta a borda de subida do botão para evitar múltiplos acionamentos.
// ==============================================================================
fbButtonEdge(CLK := xToggleButton);

// ==============================================================================
// 2. COMMAND LOGIC | LÓGICA DE COMANDO (Toggle via XOR)
// EN: Inverts the motor state securely at every single button click.
// PT: Inverte o estado do motor de forma segura a cada clique do operador.
// ==============================================================================
xMotorState := fbButtonEdge.Q XOR xMotorState;

// ==============================================================================
// 3. SAFETY INTERLOCKS | INTERTRAVAMENTOS DE SEGURANÇA (Fail-Safe)
// EN: If Emergency (FALSE) OR Thermal (FALSE), stop the motor. Priority to Reset.
// PT: Se Emergência (FALSE) OU Térmico (FALSE), desliga o motor. Prioridade ao Reset.
// ==============================================================================
IF NOT xEmergencyStop_NC OR NOT xThermalOK_NC THEN
    xMotorState := FALSE;
END_IF;

// ==============================================================================
// 4. OUTPUT MAPPING | MAPEAMENTO DAS SAÍDAS (HMI Indicators)
// EN: Map the internal states to the physical outputs and HMI lamps.
// PT: Mapeia os estados internos para as saídas físicas e lâmpadas da tela.
// ==============================================================================
xMotorCommand        := xMotorState;           // Green Light | Luz Verde
xEmergencyAlarmLight := NOT xEmergencyStop_NC; // Red Light   | Luz Vermelha
xThermalAlarmLight   := NOT xThermalOK_NC;     // Yellow Light| Luz Amarela
