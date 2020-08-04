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


(define_automaton "bsg_vanilla")
(define_cpu_unit "bsg_vanilla_alu" "bsg_vanilla")

;; ALU
(define_insn_reservation "bsg_vanilla_alu" 1
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "unknown,const,arith,shift,slt,multi,auipc,nop,logical,move"))
  "bsg_vanilla_alu")

;; remote load
(define_insn_reservation "bsg_vanilla_remote_load" 32
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "load,fpload")
       (eq_attr "remote_mem_op" "yes"))
  "bsg_vanilla_alu")

;; integer local load
(define_insn_reservation "bsg_vanilla_load" 2
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "load")
       (eq_attr "remote_mem_op" "no"))
  "bsg_vanilla_alu")

;; FP local load
(define "bsg_vanilla_fpload" 3
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "fpload")
       (eq_attr "remote_mem_op" "no"))
  "bsg_vanilla_alu")

;; store
(define_insn_reservation "bsg_vanilla_store" 1
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "store,fpstore"))
  "bsg_vanilla_alu")

;; branch
(define_insn_reservation "bsg_vanilla_branch" 1
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "branch,jump,call"))
  "bsg_vanilla_alu")

;; imul
(define_insn_reservation "bsg_vanilla_imuldiv" 33
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "imul,idiv"))
  "bsg_vanilla_alu*33")

;; i2f 
(define_insn_reservation "bsg_vanilla_i2f" 5
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "mtc"))
  "bsg_vanilla_alu")

;; f2i
(define_insn_reservation "bsg_vanilla_f2i" 1
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "mfc"))
  "bsg_vanilla_alu")

;; fpu_float
;; note: WAW hazards for FPU not currently modeled.
;; mostly it applies after branch.
;; The instruction spends one cycles in FP_EXE; 3 cycles in FPU; lastly, 1 cycles in FP_WB.
;; FP_EXE -> F -> P -> U -> FP_WB
;; Therefore, total 5 cycles of latency. (similarly for i2f)
(define_insn_reservation "bsg_vanilla_fpu_float" 5
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "fadd,fmul"))
  "bsg_vanilla_alu")


;; fcmp
(define_insn_reservation "bsg_vanilla_fcmp" 1
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "fcmp"))
  "bsg_vanilla_alu")

;; not exactly sure about the direction of fmove
;; we just give 5 as the worst case (i -> f)
(define_insn_reservation "bsg_vanilla_fmove" 5
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "fmove"))
  "bsg_vanilla_alu")
