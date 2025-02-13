;; Generic DFA-based pipeline description for RISC-V targets.
;; Copyright (C) 2011-2019 Free Software Foundation, Inc.
;; Contributed by Andrew Waterman (andrew@sifive.com).
;; Based on MIPS target for GNU compiler.

;; This file is part of GCC.

;; GCC is free software; you can redistribute it and/or modify it
;; under the terms of the GNU General Public License as published
;; by the Free Software Foundation; either version 3, or (at your
;; option) any later version.

;; GCC is distributed in the hope that it will be useful, but WITHOUT
;; ANY WARRANTY; without even the implied warranty of MERCHANTABILITY
;; or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public
;; License for more details.

;; You should have received a copy of the GNU General Public License
;; along with GCC; see the file COPYING3.  If not see
;; <http://www.gnu.org/licenses/>.


(define_automaton "bsg_vanilla_2020")
(define_cpu_unit "bsg_vanilla_2020_alu" "bsg_vanilla_2020")
(define_cpu_unit "bsg_vanilla_2020_idiv" "bsg_vanilla_2020")
(define_cpu_unit "bsg_vanilla_2020_fdiv" "bsg_vanilla_2020")


;; ALU
(define_insn_reservation "bsg_vanilla_2020_int" 1
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "unknown,const,arith,shift,slt,multi,auipc,nop,logical,move"))
  "bsg_vanilla_2020_alu")


;; remote load
(define_insn_reservation "bsg_vanilla_2020_remote_load" 32
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "load,fpload")
       (eq_attr "remote_mem_op" "yes"))
  "bsg_vanilla_2020_alu")

;; integer local load
(define_insn_reservation "bsg_vanilla_2020_load" 2
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "load")
       (eq_attr "remote_mem_op" "no"))
  "bsg_vanilla_2020_alu")


;; FP local load
(define_insn_reservation "bsg_vanilla_2020_fpload" 3
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "fpload")
       (eq_attr "remote_mem_op" "no"))
  "bsg_vanilla_2020_alu")


;; store
(define_insn_reservation "bsg_vanilla_2020_store" 1
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "store,fpstore"))
  "bsg_vanilla_2020_alu")


;; branch
(define_insn_reservation "bsg_vanilla_2020_branch" 1
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "branch,jump,call"))
  "bsg_vanilla_2020_alu")


;; idiv
;; idiv occupies EXE stage for 1 cycle, and the idiv unit for 33 cycles.
;; In total, it has 34 cycles of latency. 
(define_insn_reservation "bsg_vanilla_2020_idiv" 34
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "idiv"))
  "bsg_vanilla_2020_alu,bsg_vanilla_2020_idiv*33")


;; imul
(define_insn_reservation "bsg_vanilla_2020_imul" 2
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "imul"))
  "bsg_vanilla_2020_alu")

;; mulh
;; Try to deschedule because unsupported in hardware
(define_insn_reservation "bsg_vanilla_2020_mulh" 1000
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "imulh"))
  "bsg_vanilla_2020_alu")

;; i2f 
(define_insn_reservation "bsg_vanilla_2020_i2f" 3
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "mtc"))
  "bsg_vanilla_2020_alu")


;; f2i
(define_insn_reservation "bsg_vanilla_2020_f2i" 1
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "mfc"))
  "bsg_vanilla_2020_alu")


;; fpu_float
(define_insn_reservation "bsg_vanilla_2020_fpu_float" 3
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "fadd,fmul,fmadd"))
  "bsg_vanilla_2020_alu")


;; fcmp
(define_insn_reservation "bsg_vanilla_2020_fcmp" 1
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "fcmp"))
  "bsg_vanilla_2020_alu")


;; not exactly sure about the direction of fmove
;; we just give 3 as the worst case (i -> f)
;; see code gen pattern match rules in riscv.md
;; fabs, fsgnj, fneg, fmin, fmax, float, riscv_output_move(float,float)->fmv.s
(define_insn_reservation "bsg_vanilla_2020_fmove" 3
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "fmove"))
  "bsg_vanilla_2020_alu")


;; fdiv/fsqrt
;; fdiv/fsqrt occupies EXE stage for 1 cycle, and the fdiv unit for 25 cycles.
;; In total, it has 26 cycles of latency. 
(define_insn_reservation "bsg_vanilla_2020_fdiv" 26
  (and (eq_attr "tune" "bsg_vanilla_2020")
       (eq_attr "type" "fdiv,fsqrt"))
  "bsg_vanilla_2020_alu,bsg_vanilla_2020_fdiv*25")
