# Постановка задачи

Обработать исключение Breakpoint выводом строки "Breakpoint instruction". Настроить ресет вектор и вектор обработки прерываний на 0x400 и 0x200 соответственно. Проверить работу программы на примере isa/rv32mi/illegal.S

| Номер варианта  | Вид исключения | Тест | Reset Vector | Trap Vector | Обработчик |
| --- | --- | --- | --- | --- |  --- |
| 1 | Illegal instruction | isa/rv32mi/sbreak.S | 0x400 | 0x20 | Вывод строки «illegal» |

# Ход работы

1. Создан рабочий репозиторий - fork от репозитория SCR1 https://github.com/syntacore/scr1

---

2. До внесения изменений проект запускается.

---

3.  Отредактирован список тестов, чтобы на выполнение остался только тест по варианту задания.


В файле со списком тестов *rv32_tests.inc* оставлен только тест *illegal.S*.
Заданы переменные PATH и VERILATOR_ROOT.
```
make clean
make TARGETS="riscv_isa"
```

Вывод консоли:
```
...


---Test:                      illegal.hex
Test passed

#--------------------------------------
# Summary: 1/1 tests passed
#--------------------------------------

- /home/danis/git/scr1/src/tb/scr1_top_tb_runtests.sv:199: Verilog $finish
Simulation performed on Verilator 4.223 devel rev v4.222-59-ge7dc2de1 
                          Test               | build | simulation 
                     illegal.hex		OK	  PASS 

                    
```

---

4. Модифицирована обработку исключений trap_vector в файле ./sim/tests/common/*riscv_macros.h*
5. В файле ./src/includes/*scr1_arch_description.svh* параметры ядра *Reset Vector* и *Trap Vector* установлены в соответствии с вариантом задания (0x400, 0x200).
6. Изменен linker-скрипт ./sim/tests/common/*link.ld*

```
...

---Test:                      illegal.hex
illegalTest passed

#--------------------------------------
# Summary: 1/1 tests passed
#--------------------------------------

- /home/danis/git/scr1/src/tb/scr1_top_tb_runtests.sv:199: Verilog $finish
Simulation performed on Verilator 4.223 devel rev v4.222-59-ge7dc2de1 
                          Test               | build | simulation 
                     illegal.hex		OK	  PASS 

```

Можно видеть, что при обработке исключения было выведено сообщение "illegal is Detect".

Также в dump-файле *sbreak.dump* адреса при `<_start>` и `<trap_vector>` соответствуют указанным в задании *Reset Vector* и *Trap Vector* соответственно.
7. Также была запущена команда 
```
make run_verilator_wf TARGETS="riscv_isa" TRACE=1.
```
с помощью которой был сгененрирован wave form.

Папка results содержит следующие файлы sbreak.dump, sim_results.txt, test_results.txt, tracelog_core_0.log