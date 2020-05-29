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
(define_cpu_unit "bsg_vanilla_imuldiv" "bsg_vanilla")
(define_cpu_unit "bsg_vanilla_fdivsqrt" "bsg_vanilla")

(define_insn_reservation "bsg_vanilla_alu" 1
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "unknown,const,arith,shift,slt,multi,auipc,nop,logical,move"))
  "bsg_vanilla_alu")

(define_insn_reservation "bsg_vanilla_remote_load" 32
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "load,fpload")
       (eq_attr "remote_mem_op" "yes"))
  "bsg_vanilla_alu")

(define_insn_reservation "bsg_vanilla_load" 3
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "load,fpload")
       (eq_attr "remote_mem_op" "no"))
  "bsg_vanilla_alu")

(define_insn_reservation "bsg_vanilla_store" 1
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "store,fpstore"))
  "bsg_vanilla_alu")

(define_insn_reservation "bsg_vanilla_xfer" 3
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "mfc,mtc,fcvt,fmove,fcmp"))
  "bsg_vanilla_alu")

(define_insn_reservation "bsg_vanilla_branch" 1
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "branch,jump,call"))
  "bsg_vanilla_alu")

(define_insn_reservation "bsg_vanilla_imul" 10
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "imul"))
  "bsg_vanilla_imuldiv*10")

(define_insn_reservation "bsg_vanilla_idivsi" 34
  (and (eq_attr "tune" "bsg_vanilla")
       (and (eq_attr "type" "idiv")
	    (eq_attr "mode" "SI")))
  "bsg_vanilla_imuldiv*34")

(define_insn_reservation "bsg_vanilla_idivdi" 66
  (and (eq_attr "tune" "bsg_vanilla")
       (and (eq_attr "type" "idiv")
	    (eq_attr "mode" "DI")))
  "bsg_vanilla_imuldiv*66")

(define_insn_reservation "bsg_vanilla_fmul_single" 5
  (and (eq_attr "tune" "bsg_vanilla")
       (and (eq_attr "type" "fadd,fmul,fmadd")
	    (eq_attr "mode" "SF")))
  "bsg_vanilla_alu")

(define_insn_reservation "bsg_vanilla_fmul_double" 7
  (and (eq_attr "tune" "bsg_vanilla")
       (and (eq_attr "type" "fadd,fmul,fmadd")
	    (eq_attr "mode" "DF")))
  "bsg_vanilla_alu")

(define_insn_reservation "bsg_vanilla_fdiv" 20
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "fdiv"))
  "bsg_vanilla_fdivsqrt*20")

(define_insn_reservation "bsg_vanilla_fsqrt" 25
  (and (eq_attr "tune" "bsg_vanilla")
       (eq_attr "type" "fsqrt"))
  "bsg_vanilla_fdivsqrt*25")
