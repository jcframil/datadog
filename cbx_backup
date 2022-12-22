--
-- PostgreSQL database dump
--

-- Dumped from database version 10.22
-- Dumped by pg_dump version 14.5 (Ubuntu 14.5-0ubuntu0.22.04.1)

SET statement_timeout = 0;
SET lock_timeout = 0;
SET idle_in_transaction_session_timeout = 0;
SET client_encoding = 'UTF8';
SET standard_conforming_strings = on;
SELECT pg_catalog.set_config('search_path', '', false);
SET check_function_bodies = false;
SET xmloption = content;
SET client_min_messages = warning;
SET row_security = off;

--
-- Name: migration; Type: SCHEMA; Schema: -; Owner: councilbox
--

CREATE SCHEMA migration;


ALTER SCHEMA migration OWNER TO councilbox;

--
-- Name: unaccent; Type: EXTENSION; Schema: -; Owner: -
--

CREATE EXTENSION IF NOT EXISTS unaccent WITH SCHEMA public;


--
-- Name: EXTENSION unaccent; Type: COMMENT; Schema: -; Owner: 
--

COMMENT ON EXTENSION unaccent IS 'text search dictionary that removes accents';


--
-- Name: uuid-ossp; Type: EXTENSION; Schema: -; Owner: -
--

CREATE EXTENSION IF NOT EXISTS "uuid-ossp" WITH SCHEMA public;


--
-- Name: EXTENSION "uuid-ossp"; Type: COMMENT; Schema: -; Owner: 
--

COMMENT ON EXTENSION "uuid-ossp" IS 'generate universally unique identifiers (UUIDs)';


--
-- Name: enum_documentSignature_state; Type: TYPE; Schema: public; Owner: councilbox
--

CREATE TYPE public."enum_documentSignature_state" AS ENUM (
    'DRAFT',
    'SENT',
    'FINISHED'
);


ALTER TYPE public."enum_documentSignature_state" OWNER TO councilbox;

--
-- Name: enum_meetingParticipantConnections_online; Type: TYPE; Schema: public; Owner: councilbox
--

CREATE TYPE public."enum_meetingParticipantConnections_online" AS ENUM (
    'NO_CONNNECTED',
    'CONNECTED',
    'DISCONNECTED'
);


ALTER TYPE public."enum_meetingParticipantConnections_online" OWNER TO councilbox;

--
-- Name: enum_meetingParticipantSends_state; Type: TYPE; Schema: public; Owner: councilbox
--

CREATE TYPE public."enum_meetingParticipantSends_state" AS ENUM (
    'FAILED',
    'NOT_SENT',
    'PENDING_SHIPPING',
    'DELIVERED',
    'OPENED',
    'CLICKED',
    'SPAM',
    'INVALID_EMAIL_ADDRESS',
    'DROPPED'
);


ALTER TYPE public."enum_meetingParticipantSends_state" OWNER TO councilbox;

--
-- Name: enum_meetingParticipantSends_type; Type: TYPE; Schema: public; Owner: councilbox
--

CREATE TYPE public."enum_meetingParticipantSends_type" AS ENUM (
    'CONVENE',
    'ACCESS',
    'PASSWORD'
);


ALTER TYPE public."enum_meetingParticipantSends_type" OWNER TO councilbox;

--
-- Name: enum_meetingPlatform_email; Type: TYPE; Schema: public; Owner: councilbox
--

CREATE TYPE public."enum_meetingPlatform_email" AS ENUM (
    'HORUS'
);


ALTER TYPE public."enum_meetingPlatform_email" OWNER TO councilbox;

--
-- Name: enum_meetingPlatform_sms; Type: TYPE; Schema: public; Owner: councilbox
--

CREATE TYPE public."enum_meetingPlatform_sms" AS ENUM (
    'TWILIO'
);


ALTER TYPE public."enum_meetingPlatform_sms" OWNER TO councilbox;

--
-- Name: enum_meetingPlatform_video; Type: TYPE; Schema: public; Owner: councilbox
--

CREATE TYPE public."enum_meetingPlatform_video" AS ENUM (
    'CMP'
);


ALTER TYPE public."enum_meetingPlatform_video" OWNER TO councilbox;

--
-- Name: enum_meetings_state; Type: TYPE; Schema: public; Owner: councilbox
--

CREATE TYPE public.enum_meetings_state AS ENUM (
    'DRAFT',
    'SAVED',
    'STARTED',
    'FINISHED'
);


ALTER TYPE public.enum_meetings_state OWNER TO councilbox;

--
-- Name: copystatutes(integer, integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.copystatutes(p_origin_company_id integer, p_destination_company_id integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
DECLARE
  num_inserted_statutes int;
BEGIN
  -- Borramos los estatutos viejos de la compañía de destino
  DELETE FROM company_statutes
  WHERE company_id = p_destination_company_id;

  -- Insertamos los estatutos copiados
  WITH statute_rows AS(
    INSERT INTO company_statutes
    (company_id,title,exist_public_url,add_participants_list_to_act,exists_advance_notice_days,advance_notice_days,exists_second_call,minimum_separation_between_call,exist_quorum_assistance_first_call,quorum_assistance_first_call_way,quorum_assistance_first_call_per,exist_quorum_assistance_second_call,quorum_assistance_second_call_way,quorum_assistance_second_call_per,exists_delegated_vote,delegated_vote_way,exist_max_num_delegated_votes,max_num_delegated_votes,exists_limited_access_room,limited_access_room_minutes,exists_quality_vote,quality_vote_option,can_add_points,who_can_vote,can_reorder_points,exists_act,exists_who_was_sent_act,who_was_sent_act,who_sign_the_act,included_in_act_book,who_was_sent_act_way,can_unblock,include_participants_list)
      (SELECT
         (p_destination_company_id),title,exist_public_url,add_participants_list_to_act,exists_advance_notice_days,advance_notice_days,exists_second_call,minimum_separation_between_call,exist_quorum_assistance_first_call,quorum_assistance_first_call_way,quorum_assistance_first_call_per,exist_quorum_assistance_second_call,quorum_assistance_second_call_way,quorum_assistance_second_call_per,exists_delegated_vote,delegated_vote_way,exist_max_num_delegated_votes,max_num_delegated_votes,exists_limited_access_room,limited_access_room_minutes,exists_quality_vote,quality_vote_option,can_add_points,who_can_vote,can_reorder_points,exists_act,exists_who_was_sent_act,who_was_sent_act,who_sign_the_act,included_in_act_book,who_was_sent_act_way,can_unblock,include_participants_list
       FROM
         company_statutes
       WHERE
         company_id = p_origin_company_id)
    RETURNING 1
  )
  SELECT count(*) INTO num_inserted_statutes FROM statute_rows;
  RETURN num_inserted_statutes;
END;
$$;


ALTER FUNCTION public.copystatutes(p_origin_company_id integer, p_destination_company_id integer) OWNER TO councilbox;

--
-- Name: gettotalcelebratedcouncils(integer, integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.gettotalcelebratedcouncils(currentmonth integer, currentyear integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
	declare
		total_years integer := 0;
		total_current_year integer :=0;
	BEGIN
		select count(c.id) into total_years from councils as c inner join companies on c.company_id = companies.id where extract(YEAR FROM c.date_real_start) < currentYear and companies.demo = 0;

		select count(c.id) into total_current_year from councils as c inner join companies on c.company_id = companies.id where extract(YEAR FROM c.date_real_start) = currentYear and extract(MONTH FROM c.date_real_start) < currentMonth and companies.demo = 0;

		return total_years + total_current_year;
	END;
$$;


ALTER FUNCTION public.gettotalcelebratedcouncils(currentmonth integer, currentyear integer) OWNER TO councilbox;

--
-- Name: gettotalcensuses(integer, integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.gettotalcensuses(currentmonth integer, currentyear integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
	declare
		total_years integer := 0;
		total_current_year integer :=0;
	BEGIN
		select count(company_census.id) into total_years from company_census inner join companies on company_census.company_id = companies.id where extract(YEAR FROM company_census.creation_date) < currentYear and demo = 0;

		select count(company_census.id) into total_current_year from company_census inner join companies on company_census.company_id = companies.id where extract(YEAR FROM company_census.creation_date) = currentYear and extract(MONTH FROM company_census.creation_date) < currentMonth and demo = 0;

		return total_years + total_current_year;
	END;
$$;


ALTER FUNCTION public.gettotalcensuses(currentmonth integer, currentyear integer) OWNER TO councilbox;

--
-- Name: gettotalcompanies(integer, integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.gettotalcompanies(currentmonth integer, currentyear integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
	declare
		total_years integer := 0;
		total_current_year integer :=0;
	BEGIN
		select count(id) into total_years from companies where extract(YEAR FROM creation_date) < currentYear and demo = 0;

		select count(id) into total_current_year from companies where extract(YEAR FROM creation_date) = currentYear and extract(MONTH FROM creation_date) < currentMonth and demo = 0;

		return total_years + total_current_year;
	END;
$$;


ALTER FUNCTION public.gettotalcompanies(currentmonth integer, currentyear integer) OWNER TO councilbox;

--
-- Name: gettotalconvenedcouncils(integer, integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.gettotalconvenedcouncils(currentmonth integer, currentyear integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
	declare
		total_years integer := 0;
		total_current_year integer :=0;
	BEGIN
		select count(councils.id) into total_years FROM councils inner join companies on councils.company_id = companies.id where extract(YEAR FROM councils.date_start) < currentYear and companies.demo = 0;

		select count(councils.id) into total_current_year FROM councils inner join companies on councils.company_id = companies.id where extract(YEAR FROM councils.date_start) = currentYear and extract(MONTH FROM councils.date_start) < currentMonth and companies.demo = 0;

		return total_years + total_current_year;
	END;
$$;


ALTER FUNCTION public.gettotalconvenedcouncils(currentmonth integer, currentyear integer) OWNER TO councilbox;

--
-- Name: gettotalconvenedparticipants(integer, integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.gettotalconvenedparticipants(currentmonth integer, currentyear integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
	declare
		total_years integer := 0;
		total_current_year integer :=0;
	BEGIN
		select count(c.id) into total_years FROM councils AS c INNER JOIN council_participants AS p ON c.id = p.council_id inner join companies on c.company_id = companies.id where extract(YEAR FROM c.date_start) < currentYear and companies.demo = 0;

		select count(c.id) into total_current_year FROM councils AS c INNER JOIN council_participants AS p ON c.id = p.council_id inner join companies on c.company_id = companies.id where extract(YEAR FROM c.date_start) = currentYear and extract(MONTH FROM c.date_start) < currentMonth and companies.demo = 0;

		return total_years + total_current_year;
	END;
$$;


ALTER FUNCTION public.gettotalconvenedparticipants(currentmonth integer, currentyear integer) OWNER TO councilbox;

--
-- Name: gettotalconvenedparticipantsincelebratedcouncils(integer, integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.gettotalconvenedparticipantsincelebratedcouncils(currentmonth integer, currentyear integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
	declare
		total_years integer := 0;
		total_current_year integer :=0;
	BEGIN
		select count(p.id) into total_years from councils AS c INNER JOIN council_participants AS p ON c.id = p.council_id inner join companies on c.company_id = companies.id where extract(YEAR FROM c.date_real_start) < currentYear and companies.demo = 0;

		select count(p.id) into total_current_year from councils AS c INNER JOIN council_participants AS p ON c.id = p.council_id inner join companies on c.company_id = companies.id where extract(YEAR FROM c.date_real_start) = currentYear and extract(MONTH FROM c.date_real_start) < currentMonth and companies.demo = 0;

		return total_years + total_current_year;
	END;
$$;


ALTER FUNCTION public.gettotalconvenedparticipantsincelebratedcouncils(currentmonth integer, currentyear integer) OWNER TO councilbox;

--
-- Name: gettotalparticipantscensuses(integer, integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.gettotalparticipantscensuses(currentmonth integer, currentyear integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
	declare
		total_years integer := 0;
		total_current_year integer :=0;
	BEGIN
		select count(c.id) into total_years from company_census as c INNER JOIN census_participants AS p ON c.id = p.census_id inner join companies on c.company_id = companies.id where extract(YEAR FROM c.creation_date) < currentYear and demo = 0;

		select count(c.id) into total_current_year from company_census as c INNER JOIN census_participants AS p ON c.id = p.census_id inner join companies on c.company_id = companies.id where extract(YEAR FROM c.creation_date) = currentYear and extract(MONTH FROM c.creation_date) < currentMonth and demo = 0;

		return total_years + total_current_year;
	END;
$$;


ALTER FUNCTION public.gettotalparticipantscensuses(currentmonth integer, currentyear integer) OWNER TO councilbox;

--
-- Name: restructurecensuses(integer); Type: FUNCTION; Schema: public; Owner: councilbox
--

CREATE FUNCTION public.restructurecensuses(p_company_id integer) RETURNS integer
    LANGUAGE plpgsql
    AS $$
BEGIN
  --get waybilling associated
  with new_census as (
    insert into company_census
    (company_id, census_name, census_description, default_census, census_type, creation_date, last_edit, state)
    values(p_company_id, 'Por defecto', 'Primer censo creado automáticamente cuando se crea la empresa', 1, 0, now(), now(), 0)
    returning *
  )



  insert into census_participants
  (census_id, company_id, name, surname, position, email, percentage, dni, phone, num_participations)
    select
      (select id from new_census),
      company_id, name, surname, position, email, percentage, dni, phone, num_participations
    from
      company_participants
    where
      company_id = p_company_id;

  return 1;
END;
$$;


ALTER FUNCTION public.restructurecensuses(p_company_id integer) OWNER TO councilbox;

SET default_tablespace = '';

--
-- Name: ActAttachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."ActAttachments" (
    id integer NOT NULL,
    council_id integer NOT NULL,
    filename character varying(255) NOT NULL,
    base64 character varying(255) NOT NULL,
    filesize character varying(255) NOT NULL,
    filetype character varying(255) NOT NULL
);


ALTER TABLE public."ActAttachments" OWNER TO councilbox;

--
-- Name: ActAttachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."ActAttachments_id_seq"
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."ActAttachments_id_seq" OWNER TO councilbox;

--
-- Name: ActAttachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."ActAttachments_id_seq" OWNED BY public."ActAttachments".id;


--
-- Name: Translations; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."Translations" (
    id integer NOT NULL
);


ALTER TABLE public."Translations" OWNER TO councilbox;

--
-- Name: Translations_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."Translations_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."Translations_id_seq" OWNER TO councilbox;

--
-- Name: Translations_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."Translations_id_seq" OWNED BY public."Translations".id;


--
-- Name: accounts; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.accounts (
    name character varying(255),
    id integer NOT NULL,
    creation_date timestamp with time zone NOT NULL,
    salt character varying(255) NOT NULL,
    secret character varying(255) NOT NULL,
    apikey character varying(255) NOT NULL,
    user_type character varying(255) DEFAULT 'company'::character varying,
    expiration_date timestamp with time zone
);


ALTER TABLE public.accounts OWNER TO councilbox;

--
-- Name: accounts_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.accounts_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.accounts_id_seq OWNER TO councilbox;

--
-- Name: accounts_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.accounts_id_seq OWNED BY public.accounts.id;


--
-- Name: act; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.act (
    id integer NOT NULL,
    council_id integer,
    intro text,
    constitution text,
    conclusion text,
    email_act text,
    email_act_app text,
    sent integer DEFAULT 0,
    no_participate_census integer DEFAULT 0,
    type integer DEFAULT 0,
    document json,
    conclusion_right_column character varying,
    intro_right_column character varying,
    constitution_right_column character varying,
    svc character varying,
    sfv character varying
);


ALTER TABLE public.act OWNER TO councilbox;

--
-- Name: act_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.act_attachments (
    id integer NOT NULL,
    council_id integer,
    filename character varying,
    base64 character varying,
    filesize character varying,
    filetype character varying
);


ALTER TABLE public.act_attachments OWNER TO councilbox;

--
-- Name: act_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.act_attachments_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.act_attachments_id_seq OWNER TO councilbox;

--
-- Name: act_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.act_attachments_id_seq OWNED BY public.act_attachments.id;


--
-- Name: act_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.act_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.act_id_seq OWNER TO councilbox;

--
-- Name: act_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.act_id_seq OWNED BY public.act.id;


--
-- Name: council_participants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_participants (
    id integer NOT NULL,
    date timestamp with time zone DEFAULT now(),
    council_id integer,
    name character varying,
    "position" character varying,
    email character varying,
    phone character varying,
    dni character varying,
    type integer DEFAULT 0,
    num_participations bigint DEFAULT 0,
    surname character varying DEFAULT ''::character varying,
    uuid uuid DEFAULT public.uuid_generate_v1(),
    video_password character varying DEFAULT floor(((random() * (90000)::double precision) + (10000)::double precision)),
    real_position integer DEFAULT 0,
    language character varying DEFAULT 'es'::character varying,
    social_capital bigint DEFAULT 0,
    delegate_id integer,
    address character varying,
    city character varying,
    country character varying,
    country_state character varying,
    zipcode character varying,
    delegate_uuid uuid,
    person_or_entity integer DEFAULT 0,
    actived integer DEFAULT 1,
    face_id character varying,
    secondary_email character varying,
    external_id character varying,
    access_id character varying,
    initial_state integer DEFAULT 0,
    creator_id integer,
    role character varying,
    id_card_type character varying DEFAULT 'dni'::character varying,
    assistance_comment character varying,
    category character varying
);


ALTER TABLE public.council_participants OWNER TO councilbox;

--
-- Name: sends; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.sends (
    id integer NOT NULL,
    council_id integer,
    participant_id integer,
    send_date timestamp with time zone DEFAULT now(),
    refresh_date timestamp with time zone,
    send_type integer DEFAULT '-1'::integer,
    code character varying,
    req_code integer,
    imposition_date timestamp with time zone,
    email character varying,
    live_participant_id integer,
    content json,
    method character varying DEFAULT 'email'::character varying
);


ALTER TABLE public.sends OWNER TO councilbox;

--
-- Name: act_participants; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.act_participants AS
 SELECT p.id,
    p.delegate_id,
    p.council_id,
    (((p.name)::text || ' '::text) || (p.surname)::text) AS name,
    p.dni,
    p.type,
    p.email,
    p.phone,
    p."position",
    p.num_participations,
    p.social_capital,
    s.code,
    s.req_code,
    s.send_date,
    s.send_type
   FROM (public.council_participants p
     JOIN ( SELECT DISTINCT ON (sends.participant_id) sends.id AS send_id,
            sends.code,
            sends.req_code,
            sends.send_date,
            sends.send_type,
            sends.participant_id
           FROM public.sends
          WHERE (sends.send_type = 6)
          ORDER BY sends.participant_id, sends.send_date DESC) s ON ((p.id = s.participant_id)));


ALTER TABLE public.act_participants OWNER TO councilbox;

--
-- Name: agenda_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.agenda_attachments (
    id integer NOT NULL,
    agenda_id integer,
    filename character varying,
    base64 character varying,
    filesize character varying,
    filetype character varying,
    council_id integer,
    state integer DEFAULT 0,
    creation_date timestamp with time zone DEFAULT now(),
    document_id integer,
    hash character varying(100),
    hash_type character varying(20),
    download_id character varying
);


ALTER TABLE public.agenda_attachments OWNER TO councilbox;

--
-- Name: agenda_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.agenda_attachments_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.agenda_attachments_id_seq OWNER TO councilbox;

--
-- Name: agenda_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.agenda_attachments_id_seq OWNED BY public.agenda_attachments.id;


--
-- Name: agenda_signature_participants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.agenda_signature_participants (
    id integer NOT NULL,
    agenda_id integer NOT NULL,
    participant_id integer NOT NULL,
    status integer DEFAULT 0,
    request_id text,
    index integer
);


ALTER TABLE public.agenda_signature_participants OWNER TO councilbox;

--
-- Name: agenda_signature_participants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.agenda_signature_participants_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.agenda_signature_participants_id_seq OWNER TO councilbox;

--
-- Name: agenda_signature_participants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.agenda_signature_participants_id_seq OWNED BY public.agenda_signature_participants.id;


--
-- Name: agendas; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.agendas (
    id integer NOT NULL,
    council_id integer,
    agenda_subject character varying,
    subject_type integer,
    description text,
    date_start timestamp with time zone,
    state integer DEFAULT 0,
    positive_votings bigint DEFAULT 0,
    negative_votings bigint DEFAULT 0,
    abstention_votings bigint DEFAULT 0,
    comment text,
    public integer,
    total_votings bigint DEFAULT 0,
    date_start_votation timestamp with time zone,
    date_end_votation timestamp with time zone,
    date_end timestamp with time zone,
    positive_manual bigint DEFAULT 0,
    negative_manual bigint DEFAULT 0,
    abstention_manual bigint DEFAULT 0,
    total_manual bigint DEFAULT 0,
    present_census bigint DEFAULT 0,
    remote_census bigint DEFAULT 0,
    current_remote_census bigint DEFAULT 0,
    majority double precision DEFAULT 0,
    no_participate_census bigint DEFAULT 0,
    order_index integer,
    new_column integer,
    num_positive_votings integer DEFAULT 0,
    num_negative_votings integer DEFAULT 0,
    num_abstention_votings integer DEFAULT 0,
    num_total_votings integer DEFAULT 0,
    num_positive_manual integer DEFAULT 0,
    num_negative_manual integer DEFAULT 0,
    num_abstention_manual integer DEFAULT 0,
    num_total_manual integer DEFAULT 0,
    num_present_census integer DEFAULT 0,
    num_remote_census integer DEFAULT 0,
    num_current_remote_census integer DEFAULT 0,
    num_no_participate_census integer DEFAULT 0,
    point_state integer DEFAULT 0,
    voting_state integer DEFAULT 0,
    sortable integer DEFAULT 1,
    majority_type integer DEFAULT 0,
    no_vote_votings bigint DEFAULT 0,
    num_no_vote_votings integer DEFAULT 0,
    no_vote_manual bigint DEFAULT 0,
    num_no_vote_manual integer DEFAULT 0,
    majority_divider integer DEFAULT 3,
    social_capital_present bigint,
    social_capital_remote bigint,
    social_capital_current_remote bigint,
    social_capital_no_participate bigint,
    external_id character varying,
    comment_right_column character varying,
    default_vote integer DEFAULT '-1'::integer,
    signature_status integer,
    required integer DEFAULT 0,
    pre_instructions character varying,
    post_instructions character varying,
    cloned_from integer
);


ALTER TABLE public.agendas OWNER TO councilbox;

--
-- Name: COLUMN agendas.public; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.agendas.public IS '0 --> false
1 --> true';


--
-- Name: agendas_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.agendas_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.agendas_id_seq OWNER TO councilbox;

--
-- Name: agendas_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.agendas_id_seq OWNED BY public.agendas.id;


--
-- Name: api_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.api_logs (
    id integer NOT NULL,
    user_id integer,
    text text,
    operation character varying(255),
    ip character varying(255),
    date timestamp without time zone,
    variables text,
    type character varying(25)
);


ALTER TABLE public.api_logs OWNER TO councilbox;

--
-- Name: api_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.api_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.api_logs_id_seq OWNER TO councilbox;

--
-- Name: api_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.api_logs_id_seq OWNED BY public.api_logs.id;


--
-- Name: councils; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.councils (
    id integer NOT NULL,
    company_id integer,
    business_name character varying,
    tin character varying,
    name character varying,
    date_start timestamp with time zone,
    country character varying,
    country_state character varying,
    city character varying,
    zipcode character varying,
    header_logo text,
    convene_text text,
    send_date timestamp with time zone,
    state integer DEFAULT 0,
    date_end timestamp with time zone,
    street character varying,
    email_text text,
    date_real_start timestamp with time zone,
    duration character varying,
    current integer,
    email_app character varying,
    weighted_voting integer DEFAULT 0,
    council_type integer DEFAULT 0,
    limit_date_response timestamp with time zone,
    security_type integer DEFAULT 0,
    security_key character varying,
    shortname character varying,
    president character varying,
    secretary character varying,
    votation_type integer DEFAULT 1,
    send_points_mode integer DEFAULT 1,
    has_limit_date integer DEFAULT 0,
    full_video_record integer DEFAULT 0,
    quorum_prototype integer DEFAULT 0,
    prototype integer DEFAULT 4,
    date_start_2nd_call timestamp with time zone,
    prototype_first_call_quorum double precision,
    prototype_second_call_quorum double precision,
    video_email_template text,
    security_email_template text,
    video_emails_date timestamp with time zone,
    approve_act_draft integer DEFAULT 0,
    confirm_assistance integer DEFAULT 0,
    selected_census_id integer,
    video_mode integer,
    active integer DEFAULT 1,
    video_recoding_initialized integer DEFAULT 0,
    council_started integer DEFAULT 0,
    date_open_room character varying,
    first_or_second_convene integer DEFAULT 1,
    needed_quorum bigint,
    current_quorum bigint,
    satisfy_quorum integer DEFAULT 1,
    no_celebrate_comment character varying,
    open_room_type integer DEFAULT 0,
    proposed_act_sent integer DEFAULT 0,
    logo character varying,
    creation_date timestamp with time zone DEFAULT now(),
    step integer DEFAULT 1,
    act_point_majority_type integer DEFAULT 1,
    act_point_majority double precision DEFAULT 0,
    act_point_quorum double precision DEFAULT 0,
    language character varying DEFAULT 'es'::character varying,
    remote_celebration integer DEFAULT 0,
    creator_id integer,
    convene_user_id integer,
    send_act_date timestamp with time zone,
    video_recording integer DEFAULT 0,
    convene_send_date timestamp with time zone,
    act_point_quorum_type integer DEFAULT '-1'::integer,
    act_point_majority_divider integer DEFAULT 3,
    act_point_quorum_divider integer DEFAULT 3,
    auto_close integer DEFAULT 0,
    close_date timestamp with time zone,
    preconvene_user_id integer,
    preconvene_send_date timestamp with time zone,
    president_id integer,
    secretary_id integer,
    quality_vote_id integer,
    quality_vote_name character varying,
    wall_active integer DEFAULT 1,
    uuid uuid DEFAULT public.uuid_generate_v4() NOT NULL,
    public_convene integer DEFAULT 0 NOT NULL,
    price double precision,
    price_observations character varying,
    timezone integer DEFAULT 60,
    present_vote_overwrite integer DEFAULT 1,
    convene_footer text,
    subdomain character varying,
    initial_quorum bigint,
    custom_convene boolean DEFAULT false,
    contact_email character varying,
    endpoint character varying,
    ask_word_menu boolean DEFAULT true,
    external_id character varying,
    support_email character varying(100),
    user_schedule_id integer,
    act_visible boolean,
    date_real_end timestamp with time zone,
    treasury_shares bigint,
    comment text,
    display_name character varying,
    observations text
);


ALTER TABLE public.councils OWNER TO councilbox;

--
-- Name: COLUMN councils.state; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.councils.state IS ' -1  -->   blocked
  0  -->   planned
  1  -->   draft
  2  -->   started
  3  -->   finished';


--
-- Name: COLUMN councils.current; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.councils.current IS '0 --> false
1 --> true';


--
-- Name: COLUMN councils.send_points_mode; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.councils.send_points_mode IS '0 --> All; 
1 --> Connected;';


--
-- Name: COLUMN councils.full_video_record; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.councils.full_video_record IS '0 --> No, no record full council; 
1 --> Yes, record full council;';


--
-- Name: COLUMN councils.prototype; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.councils.prototype IS '0 --> General Assembly; 
1 --> Extraordinary Assembly; 
2 --> Board Directors; 
3 --> Meeting';


--
-- Name: live_participants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.live_participants (
    id integer NOT NULL,
    delegate_id integer,
    state integer DEFAULT 0,
    audio boolean DEFAULT true,
    video boolean DEFAULT true,
    council_id integer,
    name character varying,
    "position" character varying,
    email character varying,
    phone character varying,
    dni character varying,
    date timestamp with time zone DEFAULT now(),
    type integer DEFAULT 0,
    participant_id integer,
    online integer DEFAULT 0,
    request_word integer DEFAULT 0,
    num_participations bigint DEFAULT 0,
    surname character varying DEFAULT ''::character varying,
    uuid uuid,
    assistance_comment text,
    assistance_last_date_confirmed timestamp with time zone,
    assistance_intention integer DEFAULT 0,
    video_password character varying DEFAULT floor(((random() * (90000)::double precision) + (10000)::double precision)),
    blocked integer DEFAULT 0,
    last_date_connection timestamp with time zone,
    video_mode integer,
    first_login_date timestamp with time zone,
    first_login_current_point_id integer,
    language character varying DEFAULT 'es'::character varying,
    signed integer DEFAULT 0,
    social_capital bigint DEFAULT 0,
    address character varying,
    city character varying,
    country character varying,
    country_state character varying,
    zipcode character varying,
    delegate_uuid uuid,
    actived integer DEFAULT 1,
    person_or_entity integer DEFAULT 0,
    has_voted boolean DEFAULT false,
    votationfinish_date timestamp with time zone,
    ip character varying,
    user_agent character varying,
    face_id character varying,
    secondary_email character varying,
    external_id character varying,
    vote_denied boolean DEFAULT false,
    vote_denied_reason character varying,
    access_id character varying,
    last_request_date timestamp with time zone,
    present_at_the_start boolean DEFAULT false,
    video_id character varying,
    requesting_identification integer DEFAULT 0,
    role character varying,
    id_card_type character varying DEFAULT 'dni'::character varying,
    category character varying
);


ALTER TABLE public.live_participants OWNER TO councilbox;

--
-- Name: COLUMN live_participants.online; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.live_participants.online IS '0 --> Nunca conectado 
1 --> Alguna vez conectado 
2 --> Conectado actualmente';


--
-- Name: COLUMN live_participants.request_word; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.live_participants.request_word IS '0-> no request word 
1-> waiting to speak 
2-> speaking';


--
-- Name: assistants; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.assistants AS
 SELECT l.id,
    l.council_id,
    (((l.name)::text || ' '::text) || (l.surname)::text) AS name,
    l."position",
    l.email,
    l.dni,
    l.phone,
    l.participant_id,
    l.type,
    l.social_capital,
    l.state,
    l.online,
    l.signed,
    l.actived,
    l.uuid,
    r.id AS rid,
    (((r.name)::text || ' '::text) || (r.surname)::text) AS rname,
    r.dni AS rdni,
    r.email AS remail,
    r.phone AS rphone,
    r."position" AS rposition,
    r.participant_id AS rparticipant_id,
    r.type AS rtype,
    r.state AS rstate,
    r.online AS ronline,
    r.uuid AS ruuid,
    c.person_or_entity
   FROM (((public.live_participants l
     LEFT JOIN public.live_participants r ON ((l.delegate_id = r.id)))
     LEFT JOIN public.councils council ON ((council.id = l.council_id)))
     LEFT JOIN public.council_participants c ON ((l.participant_id = c.id)))
  WHERE ((l.type <> ALL (ARRAY[2, 4])) AND ((l.state = 7) OR (l.state = 11) OR (l.state = 8) OR (l.state = 12) OR (l.state = 9) OR (l.state = 10) OR (l.state = 5) OR ((l.state = 0) AND ((l.online = 1) OR (l.online = 2))) OR ((l.state = 2) AND ((r.state = 7) OR (r.state = 8) OR (l.state = 9) OR (r.state = 12) OR (l.state = 10) OR (r.state = 11) OR (r.state = 5) OR ((r.state = 0) AND ((r.online = 1) OR (r.online = 2)))))));


ALTER TABLE public.assistants OWNER TO councilbox;

--
-- Name: COLUMN assistants.online; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.assistants.online IS '0 --> Nunca conectado 
1 --> Alguna vez conectado 
2 --> Conectado actualmente';


--
-- Name: live_participants_signature; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.live_participants_signature (
    id integer NOT NULL,
    participant_id integer,
    data character varying,
    date timestamp with time zone DEFAULT now()
);


ALTER TABLE public.live_participants_signature OWNER TO councilbox;

--
-- Name: assistants_with_signature; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.assistants_with_signature AS
 SELECT a.id,
    a.council_id,
    a.name,
    a."position",
    a.email,
    a.dni,
    a.phone,
    lp.role,
    a.participant_id,
    a.type,
    a.social_capital,
    a.state,
    a.online,
    a.signed,
    a.actived,
    a.uuid,
    a.rid,
    a.rname,
    a.rdni,
    a.remail,
    a.rphone,
    a.rposition,
    a.rparticipant_id,
    a.rtype,
    a.rstate,
    a.ronline,
    a.ruuid,
    a.person_or_entity,
    ls.data AS signature,
    rs.data AS rsignature
   FROM (((public.assistants a
     LEFT JOIN public.live_participants_signature ls ON ((ls.participant_id = a.id)))
     LEFT JOIN public.live_participants_signature rs ON ((rs.participant_id = a.rid)))
     JOIN public.live_participants lp ON ((lp.id = a.id)));


ALTER TABLE public.assistants_with_signature OWNER TO councilbox;

--
-- Name: COLUMN assistants_with_signature.online; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.assistants_with_signature.online IS '0 --> Nunca conectado 
1 --> Alguna vez conectado 
2 --> Conectado actualmente';


--
-- Name: ballots; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.ballots (
    id integer NOT NULL,
    agenda_id integer,
    participant_id integer,
    value text,
    item_id integer,
    weight integer DEFAULT 1,
    admin integer DEFAULT 0,
    action_id uuid
);


ALTER TABLE public.ballots OWNER TO councilbox;

--
-- Name: ballots_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.ballots_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.ballots_id_seq OWNER TO councilbox;

--
-- Name: ballots_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.ballots_id_seq OWNED BY public.ballots.id;


--
-- Name: book_participant_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.book_participant_logs (
    id integer NOT NULL,
    company_id integer,
    participant_id integer,
    new_name character varying,
    new_surname character varying,
    new_dni character varying,
    new_nationality character varying,
    new_home character varying,
    old_name character varying,
    old_surname character varying,
    old_dni character varying,
    old_nationality character varying,
    old_home character varying,
    date timestamp with time zone DEFAULT now(),
    old_address character varying,
    old_city character varying,
    old_country character varying,
    old_country_state character varying,
    old_zipcode character varying,
    new_address character varying,
    new_city character varying,
    new_country character varying,
    new_country_state character varying,
    new_zipcode character varying,
    new_person_or_entity integer,
    old_person_or_entity integer,
    new_email character varying,
    new_real_position integer,
    new_language character varying,
    old_email character varying,
    old_real_position integer,
    old_language character varying,
    new_phone character varying,
    old_phone character varying,
    new_position character varying,
    old_position character varying
);


ALTER TABLE public.book_participant_logs OWNER TO councilbox;

--
-- Name: book_participant_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.book_participant_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.book_participant_logs_id_seq OWNER TO councilbox;

--
-- Name: book_participant_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.book_participant_logs_id_seq OWNED BY public.book_participant_logs.id;


--
-- Name: book_participant_transactions; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.book_participant_transactions (
    id integer NOT NULL,
    company_id integer,
    participant_id integer,
    share_id integer,
    date timestamp with time zone DEFAULT now(),
    deno character varying,
    num_participations integer,
    social_capital double precision,
    shares_from integer,
    shares_to integer,
    observations character varying,
    price double precision,
    transaction_date timestamp with time zone
);


ALTER TABLE public.book_participant_transactions OWNER TO councilbox;

--
-- Name: book_participant_transactions_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.book_participant_transactions_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.book_participant_transactions_id_seq OWNER TO councilbox;

--
-- Name: book_participant_transactions_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.book_participant_transactions_id_seq OWNED BY public.book_participant_transactions.id;


--
-- Name: book_participants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.book_participants (
    id integer NOT NULL,
    company_id integer,
    name character varying,
    surname character varying DEFAULT ''::character varying,
    dni character varying,
    nationality character varying,
    home character varying,
    language character varying DEFAULT 'es'::character varying,
    real_position integer DEFAULT 1,
    email character varying,
    phone character varying,
    type integer DEFAULT 0,
    delegate_id integer,
    uuid uuid DEFAULT public.uuid_generate_v1(),
    delegate_uuid uuid,
    address character varying,
    city character varying,
    country character varying,
    country_state character varying,
    zipcode character varying,
    "position" character varying,
    person_or_entity integer DEFAULT 0
);


ALTER TABLE public.book_participants OWNER TO councilbox;

--
-- Name: book_participants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.book_participants_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.book_participants_id_seq OWNER TO councilbox;

--
-- Name: book_participants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.book_participants_id_seq OWNED BY public.book_participants.id;


--
-- Name: book_shares; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.book_shares (
    id integer NOT NULL,
    company_id integer,
    deno character varying,
    type integer,
    num_participations integer,
    social_capital double precision,
    price double precision
);


ALTER TABLE public.book_shares OWNER TO councilbox;

--
-- Name: book_participants_recount_by_share; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.book_participants_recount_by_share AS
 SELECT sum(t.num_participations) AS num,
    ((sum(t.num_participations))::double precision * max(s.price)) AS sum,
    max(t.date) AS last_date,
    max(t.transaction_date) AS last_transaction_date,
    max(s.price) AS price,
    max((s.deno)::text) AS deno,
    t.share_id,
    t.participant_id
   FROM (public.book_participant_transactions t
     LEFT JOIN public.book_shares s ON ((t.share_id = s.id)))
  GROUP BY t.share_id, t.participant_id
  ORDER BY t.share_id, t.participant_id;


ALTER TABLE public.book_participants_recount_by_share OWNER TO councilbox;

--
-- Name: book_participants_recount; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.book_participants_recount AS
 SELECT p.id,
    p.company_id,
    (((p.name)::text || ' '::text) || (p.surname)::text) AS name,
    p.dni,
    p.nationality,
    (((r.name)::text || ' '::text) || (r.surname)::text) AS rname,
    r.dni AS rdni,
    r.nationality AS rnationality,
    p.type,
    p.email,
    p.phone,
    p.real_position,
    p.language,
    ( SELECT sum(t.num) AS num
           FROM public.book_participants_recount_by_share t
          WHERE (t.participant_id = p.id)
          GROUP BY p.id
          ORDER BY p.id) AS sum_num_participations,
    ( SELECT sum(t.sum) AS sum
           FROM public.book_participants_recount_by_share t
          WHERE (t.participant_id = p.id)
          GROUP BY p.id
          ORDER BY p.id) AS sum_social_capital
   FROM (public.book_participants p
     LEFT JOIN public.book_participants r ON ((p.delegate_id = r.id)));


ALTER TABLE public.book_participants_recount OWNER TO councilbox;

--
-- Name: book_share_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.book_share_logs (
    id integer NOT NULL,
    share_id integer,
    new_deno character varying,
    old_deno character varying,
    new_price double precision,
    old_price double precision,
    new_num_participations integer,
    old_num_participations integer,
    new_social_capital double precision,
    old_social_capital double precision,
    date timestamp with time zone DEFAULT now()
);


ALTER TABLE public.book_share_logs OWNER TO councilbox;

--
-- Name: book_share_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.book_share_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.book_share_logs_id_seq OWNER TO councilbox;

--
-- Name: book_share_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.book_share_logs_id_seq OWNED BY public.book_share_logs.id;


--
-- Name: book_shares_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.book_shares_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.book_shares_id_seq OWNER TO councilbox;

--
-- Name: book_shares_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.book_shares_id_seq OWNED BY public.book_shares.id;


--
-- Name: census_participants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.census_participants (
    id integer NOT NULL,
    name character varying,
    surname character varying DEFAULT ''::character varying,
    "position" character varying,
    email character varying,
    dni character varying,
    phone character varying,
    num_participations bigint DEFAULT 0,
    census_id integer,
    company_id integer NOT NULL,
    language character varying DEFAULT 'es'::character varying,
    real_position integer DEFAULT 1,
    social_capital bigint DEFAULT 0,
    type integer DEFAULT 0,
    delegate_id integer,
    address character varying,
    city character varying,
    country character varying,
    country_state character varying,
    zipcode character varying,
    uuid uuid DEFAULT public.uuid_generate_v1(),
    delegate_uuid uuid,
    person_or_entity integer DEFAULT 0,
    face_id character varying,
    secondary_email character varying,
    external_id character varying,
    access_id character varying,
    initial_state integer DEFAULT 0
);


ALTER TABLE public.census_participants OWNER TO councilbox;

--
-- Name: census_participants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.census_participants_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.census_participants_id_seq OWNER TO councilbox;

--
-- Name: census_participants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.census_participants_id_seq OWNED BY public.census_participants.id;


--
-- Name: certificate_points; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.certificate_points (
    id integer NOT NULL,
    certificate_id integer,
    agenda_id integer
);


ALTER TABLE public.certificate_points OWNER TO councilbox;

--
-- Name: certificate_points_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.certificate_points_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.certificate_points_id_seq OWNER TO councilbox;

--
-- Name: certificate_points_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.certificate_points_id_seq OWNED BY public.certificate_points.id;


--
-- Name: certificates; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.certificates (
    id integer NOT NULL,
    title text,
    header text,
    content text,
    footer text,
    council_id integer,
    date timestamp with time zone DEFAULT now(),
    document json
);


ALTER TABLE public.certificates OWNER TO councilbox;

--
-- Name: certificates_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.certificates_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.certificates_id_seq OWNER TO councilbox;

--
-- Name: certificates_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.certificates_id_seq OWNED BY public.certificates.id;


--
-- Name: claudia-answers; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."claudia-answers" (
    id integer NOT NULL,
    value character varying(255) NOT NULL,
    question_id integer,
    vote_id integer
);


ALTER TABLE public."claudia-answers" OWNER TO councilbox;

--
-- Name: claudia-answers_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."claudia-answers_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."claudia-answers_id_seq" OWNER TO councilbox;

--
-- Name: claudia-answers_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."claudia-answers_id_seq" OWNED BY public."claudia-answers".id;


--
-- Name: claudia-poll-questions; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."claudia-poll-questions" (
    id integer NOT NULL,
    creation_data timestamp with time zone DEFAULT now(),
    poll_id integer,
    label character varying(255),
    max_selections integer DEFAULT 1,
    min_selections integer DEFAULT 1,
    description character varying
);


ALTER TABLE public."claudia-poll-questions" OWNER TO councilbox;

--
-- Name: claudia-poll-questions_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."claudia-poll-questions_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."claudia-poll-questions_id_seq" OWNER TO councilbox;

--
-- Name: claudia-poll-questions_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."claudia-poll-questions_id_seq" OWNED BY public."claudia-poll-questions".id;


--
-- Name: claudia-poll-voters; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."claudia-poll-voters" (
    id integer NOT NULL,
    creation_data timestamp with time zone DEFAULT now(),
    vote_id integer,
    poll_id integer,
    age_range character varying(255),
    type character varying(255),
    email character varying(255)
);


ALTER TABLE public."claudia-poll-voters" OWNER TO councilbox;

--
-- Name: claudia-poll-voters_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."claudia-poll-voters_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."claudia-poll-voters_id_seq" OWNER TO councilbox;

--
-- Name: claudia-poll-voters_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."claudia-poll-voters_id_seq" OWNED BY public."claudia-poll-voters".id;


--
-- Name: claudia-poll-votes; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."claudia-poll-votes" (
    id integer NOT NULL,
    creation_data timestamp with time zone DEFAULT now(),
    poll_id integer,
    evidence character varying,
    code character varying(255) NOT NULL,
    used boolean DEFAULT false
);


ALTER TABLE public."claudia-poll-votes" OWNER TO councilbox;

--
-- Name: claudia-poll-votes_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."claudia-poll-votes_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."claudia-poll-votes_id_seq" OWNER TO councilbox;

--
-- Name: claudia-poll-votes_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."claudia-poll-votes_id_seq" OWNED BY public."claudia-poll-votes".id;


--
-- Name: claudia-polls; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."claudia-polls" (
    id integer NOT NULL,
    creation_data timestamp with time zone DEFAULT now(),
    name character varying(255),
    evidence character varying,
    user_id integer,
    state integer DEFAULT 0,
    date_start timestamp with time zone,
    date_end timestamp with time zone,
    type character varying,
    "hasRaffle" boolean DEFAULT false,
    code character varying,
    subdomain character varying,
    description character varying,
    voter_category boolean,
    voter_type boolean,
    raffle_text character varying,
    config json
);


ALTER TABLE public."claudia-polls" OWNER TO councilbox;

--
-- Name: claudia-polls_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."claudia-polls_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."claudia-polls_id_seq" OWNER TO councilbox;

--
-- Name: claudia-polls_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."claudia-polls_id_seq" OWNED BY public."claudia-polls".id;


--
-- Name: claudia-raffles; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."claudia-raffles" (
    id integer NOT NULL,
    poll_id integer,
    vote_id integer,
    winner character varying(255),
    hash character varying
);


ALTER TABLE public."claudia-raffles" OWNER TO councilbox;

--
-- Name: claudia-raffles_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."claudia-raffles_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."claudia-raffles_id_seq" OWNER TO councilbox;

--
-- Name: claudia-raffles_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."claudia-raffles_id_seq" OWNED BY public."claudia-raffles".id;


--
-- Name: claudia-users; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."claudia-users" (
    password character varying(255),
    type integer DEFAULT 0,
    username character varying(255),
    name character varying(255),
    status integer DEFAULT 0,
    surname character varying(255),
    id integer NOT NULL,
    salt character varying(255),
    change_pwd integer DEFAULT 0,
    email character varying(255),
    language character varying(255) DEFAULT 'es'::character varying,
    last_connection_date timestamp with time zone,
    register_date timestamp with time zone DEFAULT now(),
    phone character varying(255),
    organization integer
);


ALTER TABLE public."claudia-users" OWNER TO councilbox;

--
-- Name: claudia-users_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."claudia-users_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."claudia-users_id_seq" OWNER TO councilbox;

--
-- Name: claudia-users_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."claudia-users_id_seq" OWNED BY public."claudia-users".id;


--
-- Name: comments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.comments (
    id integer NOT NULL,
    council_id integer,
    agenda_id integer,
    participant_id integer,
    text character varying,
    date timestamp with time zone DEFAULT now(),
    type integer DEFAULT 0,
    active integer DEFAULT 0,
    block_user boolean DEFAULT false
);


ALTER TABLE public.comments OWNER TO councilbox;

--
-- Name: comments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.comments_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.comments_id_seq OWNER TO councilbox;

--
-- Name: comments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.comments_id_seq OWNED BY public.comments.id;


--
-- Name: companies; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.companies (
    alias character varying,
    tin character varying,
    logo character varying,
    id integer NOT NULL,
    business_name character varying,
    address character varying,
    city character varying,
    zipcode character varying,
    country character varying,
    country_state character varying,
    link_key character varying,
    creator_id integer,
    domain character varying,
    council_quorum_percentage double precision,
    agreement_quorum_percentage double precision,
    advance_notice_days integer,
    president character varying,
    secretary character varying,
    demo integer DEFAULT 0,
    type integer DEFAULT 1,
    language character varying DEFAULT 'es'::character varying,
    creation_date timestamp with time zone DEFAULT now(),
    corporation_id integer,
    apikey_id integer,
    balance double precision,
    category character varying DEFAULT 'society'::character varying,
    governing_body_type integer DEFAULT 0,
    governing_body_data json,
    file json,
    parent_id integer,
    creation_code character varying,
    external_id character varying,
    customer_code character varying,
    contact_email character varying(100),
    support_email character varying(100),
    test_url character varying,
    active integer DEFAULT 1,
    treasury_shares bigint,
    guide_link character varying,
    deleted_at timestamp without time zone,
    timezone character varying
);


ALTER TABLE public.companies OWNER TO councilbox;

--
-- Name: companies_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.companies_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.companies_id_seq OWNER TO councilbox;

--
-- Name: companies_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.companies_id_seq OWNED BY public.companies.id;


--
-- Name: company_census; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_census (
    id integer NOT NULL,
    company_id integer NOT NULL,
    census_name text,
    default_census integer DEFAULT 0 NOT NULL,
    quorum_prototype integer DEFAULT 0 NOT NULL,
    census_description character varying,
    creator_id integer,
    creation_date timestamp with time zone DEFAULT now(),
    last_edit timestamp with time zone DEFAULT now(),
    state integer DEFAULT 0
);


ALTER TABLE public.company_census OWNER TO councilbox;

--
-- Name: TABLE company_census; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON TABLE public.company_census IS 'Multiples censos para cada compañía';


--
-- Name: COLUMN company_census.default_census; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.company_census.default_census IS '0 --> False
1 --> True';


--
-- Name: COLUMN company_census.quorum_prototype; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.company_census.quorum_prototype IS '0 --> Votes
1 --> Shares';


--
-- Name: company_census_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_census_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_census_id_seq OWNER TO councilbox;

--
-- Name: company_census_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_census_id_seq OWNED BY public.company_census.id;


--
-- Name: company_corporates; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_corporates (
    id integer NOT NULL,
    company_id integer,
    logo character varying,
    convene_subject character varying,
    convene_body character varying,
    reminder_subject character varying,
    reminder_body character varying,
    reprogrammed_subject character varying,
    reprogrammed_body character varying,
    canceled_subject character varying,
    canceled_body character varying,
    room_access_subject character varying,
    room_access_body character varying,
    password_email_subject character varying,
    password_email_body character varying,
    password_sms_body character varying,
    meeting_subject character varying,
    meeting_body character varying,
    meeting_access_subject character varying,
    meeting_access_body character varying,
    act character varying
);


ALTER TABLE public.company_corporates OWNER TO councilbox;

--
-- Name: company_corporates_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_corporates_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_corporates_id_seq OWNER TO councilbox;

--
-- Name: company_corporates_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_corporates_id_seq OWNED BY public.company_corporates.id;


--
-- Name: company_documents; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_documents (
    id integer NOT NULL,
    name character varying(255) NOT NULL,
    date timestamp with time zone,
    filetype character varying(255),
    filesize integer,
    "parentFolder" integer,
    location character varying(255),
    creator_id integer,
    company_id integer,
    state integer,
    last_updated timestamp with time zone,
    type integer DEFAULT 1,
    hash character varying,
    hash_type character varying
);


ALTER TABLE public.company_documents OWNER TO councilbox;

--
-- Name: company_documents_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_documents_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_documents_id_seq OWNER TO councilbox;

--
-- Name: company_documents_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_documents_id_seq OWNED BY public.company_documents.id;


--
-- Name: company_legal_texts; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_legal_texts (
    id integer NOT NULL,
    company_id integer DEFAULT 1,
    version character varying(255),
    text text,
    type text,
    language text DEFAULT 'es'::text,
    creation_date timestamp with time zone DEFAULT now(),
    active integer DEFAULT 0,
    hash character varying(255),
    hash_type character varying(255)
);


ALTER TABLE public.company_legal_texts OWNER TO councilbox;

--
-- Name: company_legal_texts_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_legal_texts_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_legal_texts_id_seq OWNER TO councilbox;

--
-- Name: company_legal_texts_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_legal_texts_id_seq OWNED BY public.company_legal_texts.id;


--
-- Name: company_notification_templates; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_notification_templates (
    id integer NOT NULL,
    creator_id integer,
    company_id integer NOT NULL,
    show_link integer DEFAULT 1,
    custom_text text,
    action_label text,
    last_update timestamp with time zone DEFAULT now(),
    creation_date timestamp with time zone DEFAULT now(),
    language character varying(255) DEFAULT 'es'::character varying,
    subject text DEFAULT 'es'::text,
    type integer,
    notification_method character varying(255) DEFAULT 'email'::character varying,
    organization_id integer
);


ALTER TABLE public.company_notification_templates OWNER TO councilbox;

--
-- Name: company_notification_templates_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_notification_templates_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_notification_templates_id_seq OWNER TO councilbox;

--
-- Name: company_notification_templates_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_notification_templates_id_seq OWNED BY public.company_notification_templates.id;


--
-- Name: company_notifications; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_notifications (
    id integer NOT NULL,
    creator_id integer,
    company_id integer,
    action character varying(255) NOT NULL,
    description text NOT NULL,
    limit_date timestamp with time zone,
    state integer
);


ALTER TABLE public.company_notifications OWNER TO councilbox;

--
-- Name: company_notifications_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_notifications_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_notifications_id_seq OWNER TO councilbox;

--
-- Name: company_notifications_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_notifications_id_seq OWNED BY public.company_notifications.id;


--
-- Name: company_ovac; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_ovac (
    id integer NOT NULL,
    company_id integer,
    creation_date timestamp with time zone DEFAULT now() NOT NULL,
    type integer DEFAULT 0,
    config json,
    appointment_duration integer DEFAULT 30
);


ALTER TABLE public.company_ovac OWNER TO councilbox;

--
-- Name: company_ovac_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_ovac_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_ovac_id_seq OWNER TO councilbox;

--
-- Name: company_ovac_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_ovac_id_seq OWNED BY public.company_ovac.id;


--
-- Name: company_schedules; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_schedules (
    id integer NOT NULL,
    company_id integer NOT NULL,
    days character varying(255) NOT NULL,
    time_start integer NOT NULL,
    time_end integer NOT NULL,
    date_start timestamp with time zone NOT NULL,
    date_end timestamp with time zone NOT NULL,
    indefinite boolean DEFAULT false NOT NULL,
    type integer NOT NULL,
    appointment_duration integer DEFAULT 15,
    concurrent_appointments integer DEFAULT 1,
    range integer DEFAULT 1,
    recurrent boolean DEFAULT false
);


ALTER TABLE public.company_schedules OWNER TO councilbox;

--
-- Name: company_schedules_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_schedules_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_schedules_id_seq OWNER TO councilbox;

--
-- Name: company_schedules_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_schedules_id_seq OWNED BY public.company_schedules.id;


--
-- Name: company_statute_agenda_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_statute_agenda_attachments (
    id integer NOT NULL,
    agenda_id integer,
    filename character varying(255),
    base64 character varying(255),
    filesize character varying(255),
    filetype character varying(255),
    state integer DEFAULT 0,
    creation_date timestamp with time zone DEFAULT now(),
    document_id integer,
    hash character varying(255),
    hash_type character varying(255)
);


ALTER TABLE public.company_statute_agenda_attachments OWNER TO councilbox;

--
-- Name: company_statute_agenda_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_statute_agenda_attachments_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_statute_agenda_attachments_id_seq OWNER TO councilbox;

--
-- Name: company_statute_agenda_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_statute_agenda_attachments_id_seq OWNED BY public.company_statute_agenda_attachments.id;


--
-- Name: company_statute_agendas; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_statute_agendas (
    id integer NOT NULL,
    company_statute_id integer NOT NULL,
    draft_id integer,
    description text,
    title text NOT NULL,
    type integer NOT NULL,
    creation_date timestamp with time zone DEFAULT now() NOT NULL,
    creator_id integer,
    order_index integer DEFAULT 1,
    required integer DEFAULT 0,
    pre_instructions character varying,
    post_instructions character varying
);


ALTER TABLE public.company_statute_agendas OWNER TO councilbox;

--
-- Name: company_statute_agendas_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_statute_agendas_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_statute_agendas_id_seq OWNER TO councilbox;

--
-- Name: company_statute_agendas_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_statute_agendas_id_seq OWNED BY public.company_statute_agendas.id;


--
-- Name: company_statute_document_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_statute_document_attachments (
    id integer NOT NULL,
    company_statute_document_id integer,
    filename character varying(255),
    base64 character varying(255),
    filesize character varying(255),
    filetype character varying(255),
    state integer DEFAULT 0,
    creation_date timestamp with time zone DEFAULT now(),
    document_id integer,
    hash character varying(255),
    hash_type character varying(255)
);


ALTER TABLE public.company_statute_document_attachments OWNER TO councilbox;

--
-- Name: company_statute_document_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_statute_document_attachments_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_statute_document_attachments_id_seq OWNER TO councilbox;

--
-- Name: company_statute_document_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_statute_document_attachments_id_seq OWNED BY public.company_statute_document_attachments.id;


--
-- Name: company_statute_documents; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_statute_documents (
    id integer NOT NULL,
    company_statute_id integer,
    title text DEFAULT ''::text,
    description text DEFAULT ''::text,
    creation_date timestamp with time zone DEFAULT now(),
    creator_id integer,
    type integer DEFAULT 0
);


ALTER TABLE public.company_statute_documents OWNER TO councilbox;

--
-- Name: company_statute_documents_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_statute_documents_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_statute_documents_id_seq OWNER TO councilbox;

--
-- Name: company_statute_documents_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_statute_documents_id_seq OWNED BY public.company_statute_documents.id;


--
-- Name: company_statute_user_schedules; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_statute_user_schedules (
    id integer NOT NULL,
    company_statute_id integer NOT NULL,
    user_schedule_id integer NOT NULL,
    active integer DEFAULT 1,
    creator_id integer,
    created_at timestamp with time zone
);


ALTER TABLE public.company_statute_user_schedules OWNER TO councilbox;

--
-- Name: company_statute_user_schedules_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_statute_user_schedules_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_statute_user_schedules_id_seq OWNER TO councilbox;

--
-- Name: company_statute_user_schedules_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_statute_user_schedules_id_seq OWNED BY public.company_statute_user_schedules.id;


--
-- Name: company_statutes; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_statutes (
    id integer NOT NULL,
    company_id integer,
    title character varying,
    exist_public_url integer DEFAULT 0,
    add_participants_list_to_act integer DEFAULT 0,
    exists_advance_notice_days integer DEFAULT 0,
    advance_notice_days double precision DEFAULT 0,
    exists_second_call integer DEFAULT 0,
    minimum_separation_between_call double precision DEFAULT 0,
    first_call_quorum_type integer DEFAULT 0,
    first_call_quorum double precision DEFAULT 0,
    second_call_quorum_type integer DEFAULT 0,
    second_call_quorum double precision DEFAULT 0,
    exists_delegated_vote integer DEFAULT 0,
    delegated_vote_way integer DEFAULT 0,
    exist_max_num_delegated_votes integer DEFAULT 0,
    max_num_delegated_votes integer DEFAULT 0,
    exists_limited_access_room integer DEFAULT 0,
    limited_access_room_minutes integer DEFAULT 0,
    exists_quality_vote integer DEFAULT 0,
    quality_vote_option integer DEFAULT 0,
    can_add_points integer DEFAULT 0,
    who_can_vote integer,
    can_reorder_points integer DEFAULT 0,
    exists_act integer DEFAULT 0,
    exists_who_was_sent_act integer DEFAULT 0,
    who_was_sent_act integer[],
    who_sign_the_act integer[],
    included_in_act_book integer DEFAULT 0,
    who_was_sent_act_way integer DEFAULT 0,
    can_unblock integer DEFAULT 0,
    include_participants_list integer DEFAULT 0,
    exists_who_sign_the_act integer DEFAULT 0,
    prototype integer,
    intro character varying,
    constitution character varying,
    conclusion character varying,
    act_template character varying,
    convene_header character varying,
    census_id integer,
    quorum_prototype integer DEFAULT 1,
    exists_comments integer DEFAULT 1,
    first_call_quorum_divider integer DEFAULT 3,
    second_call_quorum_divider integer DEFAULT 3,
    can_edit_convene integer DEFAULT 0,
    notify_points integer DEFAULT 0,
    exist_present_with_remote_vote integer DEFAULT 0,
    convene_footer character varying,
    has_president integer DEFAULT 1,
    has_secretary integer DEFAULT 1,
    require_proxy integer DEFAULT 0,
    company_type integer DEFAULT '-1'::integer,
    language character varying DEFAULT 'all'::character varying,
    double_column_docs integer DEFAULT 0,
    proxy character varying,
    proxy_secondary character varying,
    vote_letter character varying,
    vote_letter_secondary character varying,
    intro_secondary character varying,
    conclusion_secondary character varying,
    constitution_secondary character varying,
    video_config json,
    hide_votings_recount_finished integer DEFAULT 0,
    can_early_vote integer DEFAULT 0,
    can_sense_vote_delegate integer DEFAULT 0,
    default_vote integer DEFAULT '-1'::integer,
    shareholders_portal integer DEFAULT 0,
    vote_letter_with_sense character varying,
    vote_letter_with_sense_secondary character varying,
    quorum_mode integer DEFAULT 0,
    open_all_votings_at_the_start integer DEFAULT 0,
    auto_approve_act integer DEFAULT 0,
    auto_send_act integer DEFAULT 0,
    participant_validation integer DEFAULT 0,
    let_participants_enter_after_limit integer DEFAULT 0,
    last_edited timestamp with time zone,
    hide_no_vote_button integer DEFAULT '-1'::integer,
    hide_abstention_button integer DEFAULT '-1'::integer,
    state integer DEFAULT 1,
    security_type integer DEFAULT 0,
    signature_type integer DEFAULT 0,
    full_video_record integer,
    estimated_duration integer,
    participant_can_edit_date integer DEFAULT 1,
    deleted boolean DEFAULT false,
    video_mode integer DEFAULT 0,
    access_restriction integer DEFAULT 0,
    notification_type integer DEFAULT 0,
    external_id character varying,
    celebration_mode integer DEFAULT 0
);


ALTER TABLE public.company_statutes OWNER TO councilbox;

--
-- Name: company_statutes_access_restriction; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_statutes_access_restriction (
    id integer NOT NULL,
    company_statute_id integer,
    company_id integer,
    creation_date timestamp with time zone DEFAULT now(),
    created_by integer,
    visible boolean DEFAULT true
);


ALTER TABLE public.company_statutes_access_restriction OWNER TO councilbox;

--
-- Name: company_statutes_access_restriction_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_statutes_access_restriction_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_statutes_access_restriction_id_seq OWNER TO councilbox;

--
-- Name: company_statutes_access_restriction_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_statutes_access_restriction_id_seq OWNED BY public.company_statutes_access_restriction.id;


--
-- Name: company_statutes_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_statutes_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_statutes_id_seq OWNER TO councilbox;

--
-- Name: company_statutes_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_statutes_id_seq OWNED BY public.company_statutes.id;


--
-- Name: company_tags; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_tags (
    id integer NOT NULL,
    user_id integer,
    company_id integer,
    key character varying(255) NOT NULL,
    value character varying(255) NOT NULL,
    description character varying
);


ALTER TABLE public.company_tags OWNER TO councilbox;

--
-- Name: company_tags_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.company_tags_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.company_tags_id_seq OWNER TO councilbox;

--
-- Name: company_tags_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.company_tags_id_seq OWNED BY public.company_tags.id;


--
-- Name: company_types; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.company_types (
    value integer NOT NULL,
    label character varying NOT NULL
);


ALTER TABLE public.company_types OWNER TO councilbox;

--
-- Name: corporation_drafts; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.corporation_drafts (
    id integer NOT NULL,
    user_id integer,
    title character varying,
    description character varying,
    text character varying,
    type integer,
    category integer DEFAULT 0,
    votation_type integer DEFAULT 0,
    prototype integer DEFAULT 0,
    council_type integer,
    majority_type integer DEFAULT 0,
    majority integer DEFAULT 0,
    categories integer[],
    statute_id integer,
    company_type integer,
    language character varying DEFAULT 'es'::character varying,
    creation_date timestamp with time zone DEFAULT now(),
    last_modification_date timestamp with time zone,
    corporation_id integer,
    majority_divider integer DEFAULT 3,
    governing_body_type integer DEFAULT 0,
    tags json
);


ALTER TABLE public.corporation_drafts OWNER TO councilbox;

--
-- Name: TABLE corporation_drafts; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON TABLE public.corporation_drafts IS 'type ->  dónde se usa el borrador: punto del día, introducción...
category -> categoría: aprobación, acuerdo... (En función de la categoría se podrá mostrar un quorum u otro)
votation_type -> texto (0), votación pública (1) o votación privada (3)';


--
-- Name: corporation_drafts_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.corporation_drafts_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.corporation_drafts_id_seq OWNER TO councilbox;

--
-- Name: corporation_drafts_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.corporation_drafts_id_seq OWNED BY public.corporation_drafts.id;


--
-- Name: corporation_statutes; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.corporation_statutes (
    id integer NOT NULL,
    corporation_id integer,
    company_type integer,
    title character varying(255),
    exist_public_url integer DEFAULT 0,
    add_participants_list_to_act integer DEFAULT 0,
    exists_advance_notice_days integer DEFAULT 0,
    advance_notice_days double precision DEFAULT 0,
    exists_second_call integer DEFAULT 0,
    minimum_separation_between_call double precision DEFAULT 0,
    first_call_quorum_type integer DEFAULT 0,
    first_call_quorum double precision DEFAULT 0,
    second_call_quorum_type integer DEFAULT 0,
    second_call_quorum double precision DEFAULT 0,
    exists_delegated_vote integer DEFAULT 0,
    delegated_vote_way integer DEFAULT 0,
    exist_max_num_delegated_votes integer DEFAULT 0,
    max_num_delegated_votes integer DEFAULT 0,
    exists_limited_access_room integer DEFAULT 0,
    limited_access_room_minutes integer DEFAULT 0,
    exists_quality_vote integer DEFAULT 0,
    quality_vote_option integer DEFAULT 0,
    can_add_points integer DEFAULT 0,
    who_can_vote integer,
    can_reorder_points integer DEFAULT 0,
    exists_act integer DEFAULT 0,
    exists_who_was_sent_act integer DEFAULT 0,
    who_was_sent_act character varying(255),
    who_sign_the_act character varying(255),
    included_in_act_book integer DEFAULT 0,
    who_was_sent_act_way integer DEFAULT 0,
    can_unblock integer DEFAULT 0,
    include_participants_list integer DEFAULT 0,
    exists_who_sign_the_act integer DEFAULT 0,
    prototype integer,
    intro character varying(255),
    constitution character varying(255),
    conclusion character varying(255),
    act_template character varying(255),
    convene_header character varying(255),
    quorum_prototype integer DEFAULT 1,
    exists_comments integer DEFAULT 1,
    first_call_quorum_divider integer DEFAULT 3,
    second_call_quorum_divider integer DEFAULT 3,
    can_edit_convene integer DEFAULT 0,
    notify_points integer DEFAULT 0,
    exist_present_with_remote_vote integer DEFAULT 0,
    has_president integer DEFAULT 1,
    has_secretary integer DEFAULT 1
);


ALTER TABLE public.corporation_statutes OWNER TO councilbox;

--
-- Name: corporation_statutes_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.corporation_statutes_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.corporation_statutes_id_seq OWNER TO councilbox;

--
-- Name: corporation_statutes_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.corporation_statutes_id_seq OWNED BY public.corporation_statutes.id;


--
-- Name: corporations; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.corporations (
    id integer NOT NULL,
    business_name character varying,
    tin character varying,
    address character varying,
    city character varying,
    country character varying,
    country_state character varying,
    zipcode character varying,
    logo character varying,
    creator_id integer,
    domain character varying,
    language character varying,
    creation_date timestamp with time zone DEFAULT now()
);


ALTER TABLE public.corporations OWNER TO councilbox;

--
-- Name: corporations_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.corporations_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.corporations_id_seq OWNER TO councilbox;

--
-- Name: corporations_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.corporations_id_seq OWNED BY public.corporations.id;


--
-- Name: councilTemplates; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."councilTemplates" (
    id integer NOT NULL,
    "councilId" integer NOT NULL,
    convene character varying(255),
    "conveneEmail" character varying(255),
    "conveneSubject" character varying(255),
    "reminderSubject" character varying(255),
    "rescheduledSubject" character varying(255),
    cancel character varying(255),
    "cancelSubject" character varying(255),
    "pointSend" character varying(255),
    "pointSendSubject" character varying(255),
    "roomAccess" character varying(255),
    "roomAccessSubject" character varying(255),
    "securityEmail" character varying(255),
    "securityEmailSubject" character varying(255),
    "securitySms" character varying(255),
    act character varying(255),
    "actSubject" character varying(255),
    "proposedActSubject" character varying(255),
    assistants character varying(255),
    "cbxData" character varying(255)
);


ALTER TABLE public."councilTemplates" OWNER TO councilbox;

--
-- Name: councilTemplates_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."councilTemplates_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."councilTemplates_id_seq" OWNER TO councilbox;

--
-- Name: councilTemplates_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."councilTemplates_id_seq" OWNED BY public."councilTemplates".id;


--
-- Name: council_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_attachments (
    id integer NOT NULL,
    council_id integer,
    filename character varying,
    base64 character varying,
    filesize character varying,
    filetype character varying,
    document_id integer,
    participant_id integer,
    creation_date timestamp with time zone,
    hash character varying(100) DEFAULT NULL::character varying,
    hash_type character varying(20),
    council_state integer DEFAULT 10
);


ALTER TABLE public.council_attachments OWNER TO councilbox;

--
-- Name: COLUMN council_attachments.filesize; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.council_attachments.filesize IS 'Guarda el tamaño del archivo';


--
-- Name: council_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_attachments_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_attachments_id_seq OWNER TO councilbox;

--
-- Name: council_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_attachments_id_seq OWNED BY public.council_attachments.id;


--
-- Name: council_conclusion_participan_signatures; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_conclusion_participan_signatures (
    id integer NOT NULL,
    council_id integer NOT NULL,
    participant_id integer NOT NULL,
    signature_pad_owner integer NOT NULL,
    pad_owner_type integer NOT NULL,
    status integer DEFAULT 0,
    data text,
    creation_date timestamp with time zone DEFAULT now(),
    finish_date timestamp with time zone
);


ALTER TABLE public.council_conclusion_participan_signatures OWNER TO councilbox;

--
-- Name: council_conclusion_participan_signatures_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_conclusion_participan_signatures_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_conclusion_participan_signatures_id_seq OWNER TO councilbox;

--
-- Name: council_conclusion_participan_signatures_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_conclusion_participan_signatures_id_seq OWNED BY public.council_conclusion_participan_signatures.id;


--
-- Name: council_corporates; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_corporates (
    id integer NOT NULL,
    company_id integer,
    company_corporate_id integer,
    logo character varying,
    convene_subject character varying,
    convene_body character varying,
    reminder_subject character varying,
    reminder_body character varying,
    reprogrammed_subject character varying,
    reprogrammed_body character varying,
    canceled_subject character varying,
    canceled_body character varying,
    room_access_subject character varying,
    room_access_body character varying,
    password_email_subject character varying,
    password_email_body character varying,
    password_sms_body character varying,
    council_id integer NOT NULL,
    meeting_subject character varying,
    meeting_body character varying,
    meeting_access_subject character varying,
    meeting_access_body character varying,
    act character varying
);


ALTER TABLE public.council_corporates OWNER TO councilbox;

--
-- Name: council_corporates_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_corporates_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_corporates_id_seq OWNER TO councilbox;

--
-- Name: council_corporates_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_corporates_id_seq OWNED BY public.council_corporates.id;


--
-- Name: council_delegates; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_delegates (
    id integer NOT NULL,
    council_id integer,
    participant_id integer
);


ALTER TABLE public.council_delegates OWNER TO councilbox;

--
-- Name: council_delegates_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_delegates_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_delegates_id_seq OWNER TO councilbox;

--
-- Name: council_delegates_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_delegates_id_seq OWNED BY public.council_delegates.id;


--
-- Name: council_document_attachment_signatories; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_document_attachment_signatories (
    id integer NOT NULL,
    status integer NOT NULL,
    attachment_id integer NOT NULL,
    participant_id integer NOT NULL
);


ALTER TABLE public.council_document_attachment_signatories OWNER TO councilbox;

--
-- Name: council_document_attachment_signatories_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_document_attachment_signatories_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_document_attachment_signatories_id_seq OWNER TO councilbox;

--
-- Name: council_document_attachment_signatories_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_document_attachment_signatories_id_seq OWNED BY public.council_document_attachment_signatories.id;


--
-- Name: council_document_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_document_attachments (
    id integer NOT NULL,
    council_document_id integer,
    filename character varying(255),
    base64 character varying(255),
    filesize character varying(255),
    filetype character varying(255),
    state integer DEFAULT 0,
    creation_date timestamp with time zone DEFAULT now(),
    document_id integer,
    hash character varying(255),
    hash_type character varying(255),
    participant_id integer,
    user_id integer,
    cloned_from integer
);


ALTER TABLE public.council_document_attachments OWNER TO councilbox;

--
-- Name: council_document_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_document_attachments_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_document_attachments_id_seq OWNER TO councilbox;

--
-- Name: council_document_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_document_attachments_id_seq OWNED BY public.council_document_attachments.id;


--
-- Name: council_documents; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_documents (
    id integer NOT NULL,
    council_id integer,
    type integer DEFAULT 0,
    title text DEFAULT ''::text,
    description text DEFAULT ''::text,
    creation_date timestamp with time zone DEFAULT now(),
    creator_id integer,
    order_index integer DEFAULT 1,
    cloned_from integer
);


ALTER TABLE public.council_documents OWNER TO councilbox;

--
-- Name: council_documents_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_documents_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_documents_id_seq OWNER TO councilbox;

--
-- Name: council_documents_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_documents_id_seq OWNED BY public.council_documents.id;


--
-- Name: council_new_platforms; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_new_platforms (
    council_id integer NOT NULL,
    company_id integer NOT NULL,
    room integer DEFAULT 1 NOT NULL,
    room_access integer DEFAULT 5 NOT NULL,
    security_email integer DEFAULT 5 NOT NULL,
    security_sms integer DEFAULT 4 NOT NULL,
    video integer DEFAULT 1 NOT NULL,
    id integer NOT NULL,
    emails integer DEFAULT 5,
    act integer DEFAULT 5,
    signature integer DEFAULT 7
);


ALTER TABLE public.council_new_platforms OWNER TO councilbox;

--
-- Name: council_new_platforms_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_new_platforms_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_new_platforms_id_seq OWNER TO councilbox;

--
-- Name: council_new_platforms_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_new_platforms_id_seq OWNED BY public.council_new_platforms.id;


--
-- Name: council_platforms; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_platforms (
    id integer NOT NULL,
    council_id integer,
    html_video_council character varying,
    html_video_participant character varying,
    platform_video character varying,
    video_link character varying,
    last_access_date timestamp with time zone,
    video_config json,
    video_data json,
    type character varying(20) DEFAULT 'CMP'::character varying,
    layout character varying(25) DEFAULT 'grid'::character varying
);


ALTER TABLE public.council_platforms OWNER TO councilbox;

--
-- Name: council_platforms_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_platforms_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_platforms_id_seq OWNER TO councilbox;

--
-- Name: council_platforms_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_platforms_id_seq OWNED BY public.council_platforms.id;


--
-- Name: council_portal_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_portal_attachments (
    id integer NOT NULL,
    portal_id integer NOT NULL,
    filename character varying(255) NOT NULL,
    filesize character varying(255),
    filetype character varying(255) NOT NULL,
    show_in_home boolean DEFAULT true
);


ALTER TABLE public.council_portal_attachments OWNER TO councilbox;

--
-- Name: council_portal_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_portal_attachments_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_portal_attachments_id_seq OWNER TO councilbox;

--
-- Name: council_portal_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_portal_attachments_id_seq OWNED BY public.council_portal_attachments.id;


--
-- Name: council_portals; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_portals (
    id integer NOT NULL,
    council_id integer,
    send_accept_confimation integer DEFAULT 1,
    send_refuse_confirmation integer DEFAULT 1,
    access_confirmation_text text,
    delegation_confirmation_text text,
    refused_confirmation_text text,
    name character varying(40),
    config json,
    state integer DEFAULT 0,
    last_edited timestamp with time zone,
    early_vote_confirmation text,
    translations_overrides json DEFAULT '{}'::json
);


ALTER TABLE public.council_portals OWNER TO councilbox;

--
-- Name: council_portals_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_portals_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_portals_id_seq OWNER TO councilbox;

--
-- Name: council_portals_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_portals_id_seq OWNED BY public.council_portals.id;


--
-- Name: council_statutes; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.council_statutes (
    id integer NOT NULL,
    council_id integer,
    title character varying,
    exist_public_url integer DEFAULT 0,
    add_participants_list_to_act integer DEFAULT 0,
    exists_advance_notice_days integer DEFAULT 0,
    advance_notice_days double precision DEFAULT 0,
    exists_second_call integer DEFAULT 0,
    minimum_separation_between_call double precision DEFAULT 0,
    first_call_quorum_type integer DEFAULT 0,
    first_call_quorum double precision DEFAULT 0,
    second_call_quorum_type integer DEFAULT 0,
    second_call_quorum double precision DEFAULT 0,
    exists_delegated_vote integer DEFAULT 0,
    delegated_vote_way integer DEFAULT 0,
    exist_max_num_delegated_votes integer DEFAULT 0,
    max_num_delegated_votes integer DEFAULT 0,
    exists_limited_access_room integer DEFAULT 0,
    limited_access_room_minutes integer DEFAULT 0,
    exists_quality_vote integer DEFAULT 0,
    quality_vote_option integer DEFAULT 0,
    can_add_points integer DEFAULT 0,
    who_can_vote integer,
    can_reorder_points integer DEFAULT 0,
    exists_act integer DEFAULT 0,
    exists_who_was_sent_act integer DEFAULT 0,
    who_was_sent_act_way integer DEFAULT 0,
    who_was_sent_act integer[],
    who_sign_the_act integer[],
    included_in_act_book integer DEFAULT 0,
    statute_id integer,
    can_unblock integer DEFAULT 0,
    include_participants_list integer DEFAULT 0,
    exists_who_sign_the_act integer DEFAULT 0,
    prototype integer,
    intro character varying,
    constitution character varying,
    conclusion character varying,
    act_template character varying,
    convene_header character varying,
    census_id integer,
    exists_comments integer DEFAULT 1,
    quorum_prototype integer DEFAULT 1,
    first_call_quorum_divider integer DEFAULT 3,
    second_call_quorum_divider integer DEFAULT 3,
    can_edit_convene integer DEFAULT 0,
    notify_points integer DEFAULT 0,
    exist_present_with_remote_vote integer DEFAULT 0,
    convene_footer character varying,
    has_president integer DEFAULT 1,
    has_secretary integer DEFAULT 1,
    require_proxy integer DEFAULT 0,
    double_column_docs integer DEFAULT 0,
    proxy character varying,
    proxy_secondary character varying,
    vote_letter character varying,
    vote_letter_secondary character varying,
    intro_secondary character varying,
    constitution_secondary character varying,
    conclusion_secondary character varying,
    hide_votings_recount_finished integer DEFAULT 0,
    can_early_vote integer DEFAULT 0,
    can_sense_vote_delegate integer DEFAULT 0,
    default_vote integer DEFAULT '-1'::integer,
    shareholders_portal integer DEFAULT 0,
    attendance_text character varying,
    vote_letter_with_sense character varying,
    vote_letter_with_sense_secondary character varying,
    decimal_digits integer DEFAULT 0,
    quorum_mode integer DEFAULT 0,
    open_all_votings_at_the_start integer DEFAULT 0,
    auto_approve_act integer DEFAULT 0,
    auto_send_act integer DEFAULT 0,
    participant_validation integer DEFAULT 0,
    let_participants_enter_after_limit integer DEFAULT 0,
    hide_no_vote_button integer DEFAULT '-1'::integer,
    hide_abstention_button integer DEFAULT '-1'::integer,
    estimated_duration integer DEFAULT 15,
    signature_type integer DEFAULT 0,
    participant_can_edit_date integer DEFAULT 1,
    notification_type integer DEFAULT 0,
    celebration_mode integer DEFAULT 0
);


ALTER TABLE public.council_statutes OWNER TO councilbox;

--
-- Name: council_statutes_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.council_statutes_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.council_statutes_id_seq OWNER TO councilbox;

--
-- Name: council_statutes_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.council_statutes_id_seq OWNED BY public.council_statutes.id;


--
-- Name: councils_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.councils_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.councils_id_seq OWNER TO councilbox;

--
-- Name: councils_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.councils_id_seq OWNED BY public.councils.id;


--
-- Name: recount_info; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.recount_info AS
 SELECT l.id,
    l.state,
    l.online,
    l.num_participations,
    l.social_capital,
    ld.state AS ld_state,
    ld.online AS ld_online,
    ld.type AS ld_type,
    c.send_points_mode,
    c.id AS council_id,
    l.type
   FROM ((public.live_participants l
     JOIN public.councils c ON ((c.id = l.council_id)))
     LEFT JOIN public.live_participants ld ON ((l.delegate_id = ld.id)))
  WHERE (((l.type = 0) OR (l.type = 3) OR (l.type = 4)) AND ((ld.type IS NULL) OR (ld.type <> 1)));


ALTER TABLE public.recount_info OWNER TO councilbox;

--
-- Name: COLUMN recount_info.online; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.recount_info.online IS '0 --> Nunca conectado 
1 --> Alguna vez conectado 
2 --> Conectado actualmente';


--
-- Name: COLUMN recount_info.send_points_mode; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.recount_info.send_points_mode IS '0 --> All; 
1 --> Connected;';


--
-- Name: COLUMN recount_info.council_id; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.recount_info.council_id IS '0 --> All; 
1 --> Connected;';


--
-- Name: councils_recount; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.councils_recount AS
 SELECT r.council_id,
    COALESCE(r.num_total, (0)::bigint) AS num_total,
    COALESCE(r.part_total, ((0)::bigint)::numeric) AS part_total,
    COALESCE(r.social_capital_total, ((0)::bigint)::numeric) AS social_capital_total,
    COALESCE(r.part_remote, ((0)::bigint)::numeric) AS part_remote,
    COALESCE(r.social_capital_remote, ((0)::bigint)::numeric) AS social_capital_remote,
    COALESCE(r.num_remote, (0)::bigint) AS num_remote,
    COALESCE(r.part_present, ((0)::bigint)::numeric) AS part_present,
    COALESCE(r.num_present, (0)::bigint) AS num_present,
    COALESCE(r.social_capital_present, ((0)::bigint)::numeric) AS social_capital_present,
    COALESCE(r.part_no_participate, ((0)::bigint)::numeric) AS part_no_participate,
    COALESCE(r.num_no_participate, (0)::bigint) AS num_no_participate,
    COALESCE(r.social_capital_no_participate, ((0)::bigint)::numeric) AS social_capital_no_participate,
    COALESCE(r.part_current_remote, ((0)::bigint)::numeric) AS part_current_remote,
    COALESCE(r.num_current_remote, (0)::bigint) AS num_current_remote,
    COALESCE(r.social_capital_current_remote, ((0)::bigint)::numeric) AS social_capital_current_remote,
    COALESCE((r.num_present + r.num_current_remote), (0)::bigint) AS num_right_voting,
    (COALESCE(r.part_present, ((0)::bigint)::numeric) + COALESCE(r.part_current_remote, ((0)::bigint)::numeric)) AS part_right_voting,
    (COALESCE(r.social_capital_present, ((0)::bigint)::numeric) + COALESCE(r.social_capital_current_remote, ((0)::bigint)::numeric)) AS social_capital_right_voting
   FROM ( SELECT l.council_id,
            count(l.id) AS num_total,
            sum(l.num_participations) AS part_total,
            sum(l.social_capital) AS social_capital_total,
            ( SELECT sum(lr.num_participations) AS sum
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (lr.num_participations <> 0) AND (((lr.send_points_mode = 1) AND (((lr.state = 0) AND ((lr.online = 1) OR (lr.online = 2))) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0) AND ((lr.ld_online = 1) OR (lr.ld_online = 2))))) OR ((lr.send_points_mode = 0) AND ((lr.state = 0) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0))))))) AS part_remote,
            ( SELECT sum(lr.social_capital) AS sum
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (((lr.send_points_mode = 1) AND (((lr.state = 0) AND ((lr.online = 1) OR (lr.online = 2))) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0) AND ((lr.ld_online = 1) OR (lr.ld_online = 2))))) OR ((lr.send_points_mode = 0) AND ((lr.state = 0) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0))))))) AS social_capital_remote,
            ( SELECT count(lr.id) AS count
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (lr.num_participations <> 0) AND (((lr.send_points_mode = 1) AND (((lr.state = 0) AND ((lr.online = 1) OR (lr.online = 2))) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0) AND ((lr.ld_online = 1) OR (lr.ld_online = 2))))) OR ((lr.send_points_mode = 0) AND ((lr.state = 0) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0))))))) AS num_remote,
            ( SELECT sum(lr.num_participations) AS sum
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (lr.num_participations <> 0) AND ((lr.state = 5) OR (lr.state = 7) OR (((lr.state = 4) OR (lr.state = 2)) AND ((lr.ld_state = 5) OR (lr.ld_state = 7)))))) AS part_present,
            ( SELECT sum(lr.social_capital) AS sum
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND ((lr.state = 5) OR (lr.state = 7) OR (((lr.state = 4) OR (lr.state = 2)) AND ((lr.ld_state = 5) OR (lr.ld_state = 7)))))) AS social_capital_present,
            ( SELECT count(lr.id) AS count
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (lr.num_participations <> 0) AND ((lr.state = 5) OR (lr.state = 7) OR (((lr.state = 4) OR (lr.state = 2)) AND ((lr.ld_state = 5) OR (lr.ld_state = 7)))))) AS num_present,
            ( SELECT sum(lr.num_participations) AS sum
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (lr.num_participations <> 0) AND (lr.state = 6))) AS part_no_participate,
            ( SELECT sum(lr.social_capital) AS sum
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (lr.state = 6))) AS social_capital_no_participate,
            ( SELECT count(lr.id) AS count
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (lr.state = 6))) AS num_no_participate,
            ( SELECT sum(lr.num_participations) AS sum
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (lr.num_participations <> 0) AND (((lr.send_points_mode = 0) AND ((lr.state = 0) OR (lr.state = 8) OR (lr.ld_state = 8) OR (lr.state = 12) OR (lr.ld_state = 12) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0)))) OR ((lr.send_points_mode = 1) AND (lr.state = 0) AND ((lr.online = 1) OR (lr.online = 2))) OR (lr.state = 8) OR (lr.ld_state = 8) OR (lr.state = 12) OR (lr.ld_state = 12) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0) AND ((lr.ld_online = 1) OR (lr.ld_online = 2)))))) AS part_current_remote,
            ( SELECT sum(lr.social_capital) AS sum
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (((lr.send_points_mode = 0) AND ((lr.state = 0) OR (lr.state = 8) OR (lr.ld_state = 8) OR (lr.state = 12) OR (lr.ld_state = 12) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0)))) OR ((lr.send_points_mode = 1) AND (lr.state = 0) AND ((lr.online = 1) OR (lr.online = 2))) OR (lr.state = 8) OR (lr.ld_state = 8) OR (lr.state = 12) OR (lr.ld_state = 12) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0) AND ((lr.ld_online = 1) OR (lr.ld_online = 2)))))) AS social_capital_current_remote,
            ( SELECT count(lr.id) AS count
                   FROM public.recount_info lr
                  WHERE ((lr.council_id = l.council_id) AND (((lr.send_points_mode = 0) AND ((lr.state = 0) OR (lr.state = 8) OR (lr.state = 12) OR (lr.ld_state = 8) OR (lr.ld_state = 12) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0)))) OR ((lr.send_points_mode = 1) AND (lr.state = 0) AND ((lr.online = 1) OR (lr.online = 2))) OR (lr.state = 8) OR (lr.ld_state = 8) OR (lr.state = 12) OR (lr.ld_state = 12) OR (((lr.state = 4) OR (lr.state = 2)) AND (lr.ld_state = 0) AND ((lr.ld_online = 1) OR (lr.ld_online = 2)))))) AS num_current_remote
           FROM public.live_participants l
          GROUP BY l.council_id
          ORDER BY l.council_id) r;


ALTER TABLE public.councils_recount OWNER TO councilbox;

--
-- Name: COLUMN councils_recount.council_id; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.councils_recount.council_id IS '0 --> All; 
1 --> Connected;';


--
-- Name: countries; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.countries (
    id integer NOT NULL,
    deno character varying
);


ALTER TABLE public.countries OWNER TO councilbox;

--
-- Name: countries_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.countries_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.countries_id_seq OWNER TO councilbox;

--
-- Name: countries_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.countries_id_seq OWNED BY public.countries.id;


--
-- Name: customer_survey_questions; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.customer_survey_questions (
    id integer NOT NULL,
    creation_date timestamp with time zone DEFAULT now(),
    form_type integer DEFAULT 0,
    has_comment boolean DEFAULT false,
    order_index integer DEFAULT 1,
    title text,
    active boolean DEFAULT true,
    type integer DEFAULT 0,
    question_group_id integer,
    council_with_session boolean DEFAULT false,
    council_without_session boolean DEFAULT false,
    elections boolean DEFAULT false
);


ALTER TABLE public.customer_survey_questions OWNER TO councilbox;

--
-- Name: customer_survey_questions_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.customer_survey_questions_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.customer_survey_questions_id_seq OWNER TO councilbox;

--
-- Name: customer_survey_questions_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.customer_survey_questions_id_seq OWNED BY public.customer_survey_questions.id;


--
-- Name: signatures; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.signatures (
    id integer NOT NULL,
    company_id integer NOT NULL,
    title text NOT NULL,
    description text,
    expiration_date_to_sign timestamp with time zone,
    selected_census_id integer NOT NULL,
    state integer DEFAULT 0,
    request_code integer,
    request_id text,
    request_url text,
    logo text,
    step integer DEFAULT 1,
    active integer DEFAULT 1,
    platform integer DEFAULT 8,
    column_15 integer,
    type character varying DEFAULT 'ACEP'::character varying
);


ALTER TABLE public.signatures OWNER TO councilbox;

--
-- Name: dashboard_recount; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.dashboard_recount AS
 SELECT companies.id AS company_id,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 1) AND (councils.state = 0))) AS council_draft,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 1) AND (councils.state = 3))) AS council_preconvene,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 1) AND ((councils.state = 5) OR (councils.state = 10)))) AS council_calendar,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 1) AND ((councils.state = 20) OR (councils.state = 30)))) AS council_live,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 1) AND (councils.state = 40))) AS council_writing,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 1) AND (councils.state = 60))) AS council_docu,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 1) AND ((councils.state = 70) OR (councils.state = 90)))) AS council_history,
    ( SELECT count(councils.id) AS count
           FROM (public.councils
             JOIN public.council_statutes cs ON ((councils.id = cs.council_id)))
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (cs.included_in_act_book = 1) AND (councils.active = 1) AND (councils.state = 70))) AS council_act_book,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 1) AND (councils.state = 90))) AS council_without_act,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 4) AND (councils.active = 0))) AS council_trash,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 3) AND (councils.active = 1) AND (councils.state = 0))) AS meeting_draft,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 3) AND (councils.active = 1) AND ((councils.state = 5) OR (councils.state = 10)))) AS meeting_calendar,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 3) AND (councils.active = 1) AND ((councils.state = 20) OR (councils.state = 30)))) AS meeting_live,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 3) AND (councils.active = 1) AND (councils.state = 100))) AS meeting_finished,
    ( SELECT count(councils.id) AS count
           FROM public.councils
          WHERE ((councils.company_id = companies.id) AND (councils.prototype = 3) AND (councils.active = 0))) AS meeting_trash,
    ( SELECT count(signatures.id) AS count
           FROM public.signatures
          WHERE ((signatures.company_id = companies.id) AND (signatures.active = 1) AND (signatures.state = 0))) AS signature_draft,
    ( SELECT count(signatures.id) AS count
           FROM public.signatures
          WHERE ((signatures.company_id = companies.id) AND (signatures.active = 1) AND (signatures.state = 10))) AS signature_sent,
    ( SELECT count(signatures.id) AS count
           FROM public.signatures
          WHERE ((signatures.company_id = companies.id) AND (signatures.active = 1) AND (signatures.state = 20))) AS signature_completed,
    ( SELECT count(signatures.id) AS count
           FROM public.signatures
          WHERE ((signatures.company_id = companies.id) AND (signatures.active = 1) AND (signatures.state = 30))) AS signature_archived,
    ( SELECT count(signatures.id) AS count
           FROM public.signatures
          WHERE ((signatures.company_id = companies.id) AND (signatures.active = 0))) AS signature_trash
   FROM public.companies;


ALTER TABLE public.dashboard_recount OWNER TO councilbox;

--
-- Name: delegation_proxies; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.delegation_proxies (
    id integer NOT NULL,
    participant_id integer NOT NULL,
    delegate_id integer NOT NULL,
    date timestamp with time zone,
    signature character varying,
    council_id integer NOT NULL,
    signed_by integer
);


ALTER TABLE public.delegation_proxies OWNER TO councilbox;

--
-- Name: delegation_proxies_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.delegation_proxies_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.delegation_proxies_id_seq OWNER TO councilbox;

--
-- Name: delegation_proxies_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.delegation_proxies_id_seq OWNED BY public.delegation_proxies.id;


--
-- Name: documentAttachment; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."documentAttachment" (
    id integer NOT NULL,
    "signatureId" integer NOT NULL,
    title text,
    description text,
    filename character varying(255) NOT NULL,
    base64 text,
    filesize character varying(255),
    filetype character varying(255)
);


ALTER TABLE public."documentAttachment" OWNER TO councilbox;

--
-- Name: documentAttachment_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."documentAttachment_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."documentAttachment_id_seq" OWNER TO councilbox;

--
-- Name: documentAttachment_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."documentAttachment_id_seq" OWNED BY public."documentAttachment".id;


--
-- Name: documentParticipants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."documentParticipants" (
    id integer NOT NULL,
    "signatureId" integer,
    name character varying(255) NOT NULL,
    surname character varying(255) NOT NULL,
    email character varying(255) NOT NULL,
    dni character varying(255) NOT NULL,
    uuid uuid,
    status integer,
    "requestId" text
);


ALTER TABLE public."documentParticipants" OWNER TO councilbox;

--
-- Name: documentParticipants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."documentParticipants_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."documentParticipants_id_seq" OWNER TO councilbox;

--
-- Name: documentParticipants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."documentParticipants_id_seq" OWNED BY public."documentParticipants".id;


--
-- Name: documentSignature; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."documentSignature" (
    id integer NOT NULL,
    "companyId" integer NOT NULL,
    title text NOT NULL,
    description text,
    "expirationDateToSign" timestamp with time zone,
    state public."enum_documentSignature_state" DEFAULT 'DRAFT'::public."enum_documentSignature_state",
    "requestId" text,
    active boolean DEFAULT true,
    language character varying(255)
);


ALTER TABLE public."documentSignature" OWNER TO councilbox;

--
-- Name: documentSignature_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."documentSignature_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."documentSignature_id_seq" OWNER TO councilbox;

--
-- Name: documentSignature_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."documentSignature_id_seq" OWNED BY public."documentSignature".id;


--
-- Name: draft_types; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.draft_types (
    id integer NOT NULL,
    label character varying(255),
    value integer
);


ALTER TABLE public.draft_types OWNER TO councilbox;

--
-- Name: draft_types_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.draft_types_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.draft_types_id_seq OWNER TO councilbox;

--
-- Name: draft_types_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.draft_types_id_seq OWNED BY public.draft_types.id;


--
-- Name: drafts; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.drafts (
    id integer NOT NULL,
    user_id integer,
    company_id integer,
    title character varying,
    description character varying,
    text character varying,
    type integer,
    category integer DEFAULT 0,
    votation_type integer DEFAULT 0,
    prototype integer DEFAULT 0,
    council_type integer,
    majority_type integer DEFAULT 0,
    majority integer DEFAULT 0,
    categories integer[],
    statute_id integer,
    company_type integer,
    language character varying DEFAULT 'es'::character varying,
    draft_id integer,
    creation_date timestamp with time zone DEFAULT now(),
    last_modification_date timestamp with time zone,
    corporation_id integer,
    majority_divider integer DEFAULT 3,
    governing_body_type integer DEFAULT 0,
    tags json,
    secondary_text character varying,
    active integer DEFAULT 0
);


ALTER TABLE public.drafts OWNER TO councilbox;

--
-- Name: TABLE drafts; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON TABLE public.drafts IS 'type ->  dónde se usa el borrador: punto del día, introducción...
category -> categoría: aprobación, acuerdo... (En función de la categoría se podrá mostrar un quorum u otro)
votation_type -> texto (0), votación pública (1) o votación privada (3)';


--
-- Name: drafts_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.drafts_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.drafts_id_seq OWNER TO councilbox;

--
-- Name: drafts_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.drafts_id_seq OWNED BY public.drafts.id;


--
-- Name: evidence_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.evidence_logs (
    id integer NOT NULL,
    date timestamp with time zone DEFAULT now(),
    user_id integer,
    type character varying(255),
    uuid uuid,
    participant_id integer,
    content character varying,
    council_id integer,
    cbx_evidence character varying,
    hash character varying(128) DEFAULT NULL::character varying
);


ALTER TABLE public.evidence_logs OWNER TO councilbox;

--
-- Name: evidences; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.evidences (
    id integer NOT NULL,
    ev_hash character varying(255),
    minted boolean DEFAULT false,
    display_name character varying(255),
    evidence character varying(255),
    type character varying(255),
    original_evidence character varying(255),
    creator_id integer,
    visibility_mask character varying(255),
    "from" character varying(255),
    register_date timestamp with time zone DEFAULT now()
);


ALTER TABLE public.evidences OWNER TO councilbox;

--
-- Name: evidences_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.evidences_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.evidences_id_seq OWNER TO councilbox;

--
-- Name: evidences_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.evidences_id_seq OWNED BY public.evidences.id;


--
-- Name: feature_exceptions; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.feature_exceptions (
    id integer NOT NULL,
    feature_name character varying,
    company_id integer NOT NULL,
    active boolean DEFAULT false NOT NULL
);


ALTER TABLE public.feature_exceptions OWNER TO councilbox;

--
-- Name: feature_exceptions_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.feature_exceptions_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.feature_exceptions_id_seq OWNER TO councilbox;

--
-- Name: feature_exceptions_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.feature_exceptions_id_seq OWNED BY public.feature_exceptions.id;


--
-- Name: features; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.features (
    id integer NOT NULL,
    name character varying(255) NOT NULL,
    active boolean DEFAULT false NOT NULL,
    value json
);


ALTER TABLE public.features OWNER TO councilbox;

--
-- Name: features_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.features_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.features_id_seq OWNER TO councilbox;

--
-- Name: features_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.features_id_seq OWNED BY public.features.id;


--
-- Name: languages; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.languages (
    id integer NOT NULL,
    "desc" character varying,
    column_name character varying,
    image character varying
);


ALTER TABLE public.languages OWNER TO councilbox;

--
-- Name: languages_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.languages_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.languages_id_seq OWNER TO councilbox;

--
-- Name: languages_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.languages_id_seq OWNED BY public.languages.id;


--
-- Name: lastSendAct; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public."lastSendAct" AS
 SELECT DISTINCT ON (sends.live_participant_id) sends.id,
    sends.code,
    sends.req_code AS "reqCode",
    sends.send_date AS "sendDate",
    sends.send_type AS "sendType",
    sends.refresh_date AS "refreshDate",
    sends.imposition_date AS "impositionDate",
    sends.email,
    sends.council_id AS "councilId",
    sends.live_participant_id AS "participantId"
   FROM public.sends
  WHERE (sends.send_type = 6)
  ORDER BY sends.live_participant_id, sends.send_date DESC;


ALTER TABLE public."lastSendAct" OWNER TO councilbox;

--
-- Name: lastSendConvene; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public."lastSendConvene" AS
 SELECT DISTINCT ON (sends.participant_id) sends.id,
    sends.code,
    sends.req_code AS "reqCode",
    sends.send_date AS "sendDate",
    sends.send_type AS "sendType",
    sends.refresh_date AS "refreshDate",
    sends.imposition_date AS "impositionDate",
    sends.email,
    sends.council_id AS "councilId",
    live_participants.id AS "participantId"
   FROM (public.sends
     JOIN public.live_participants ON ((sends.participant_id = live_participants.participant_id)))
  WHERE (((sends.send_type = 0) OR (sends.send_type = 2)) AND (live_participants.actived = 1))
  ORDER BY sends.participant_id, sends.send_date DESC;


ALTER TABLE public."lastSendConvene" OWNER TO councilbox;

--
-- Name: lastSendCredentials; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public."lastSendCredentials" AS
 SELECT DISTINCT ON (sends.live_participant_id) sends.id,
    sends.code,
    sends.req_code AS "reqCode",
    sends.send_date AS "sendDate",
    sends.send_type AS "sendType",
    sends.refresh_date AS "refreshDate",
    sends.imposition_date AS "impositionDate",
    sends.email,
    sends.council_id AS "councilId",
    sends.live_participant_id AS "participantId"
   FROM public.sends
  WHERE (sends.send_type = 4)
  ORDER BY sends.live_participant_id, sends.send_date DESC;


ALTER TABLE public."lastSendCredentials" OWNER TO councilbox;

--
-- Name: licences; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.licences (
    id integer NOT NULL,
    date timestamp with time zone DEFAULT now(),
    expiration_date timestamp with time zone DEFAULT now(),
    start_date timestamp with time zone DEFAULT now(),
    organization_id integer NOT NULL,
    code character varying(255)
);


ALTER TABLE public.licences OWNER TO councilbox;

--
-- Name: licenses; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.licenses (
    id integer NOT NULL,
    date timestamp with time zone DEFAULT now(),
    expiration_date timestamp with time zone DEFAULT now(),
    start_date timestamp with time zone DEFAULT now(),
    organization_id integer NOT NULL,
    code character varying(255),
    type integer DEFAULT 0,
    user_id integer,
    enabled boolean DEFAULT true,
    last_used timestamp with time zone
);


ALTER TABLE public.licenses OWNER TO councilbox;

--
-- Name: licences_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.licences_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.licences_id_seq OWNER TO councilbox;

--
-- Name: licences_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.licences_id_seq OWNED BY public.licenses.id;


--
-- Name: licences_id_seq1; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.licences_id_seq1
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.licences_id_seq1 OWNER TO councilbox;

--
-- Name: licences_id_seq1; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.licences_id_seq1 OWNED BY public.licences.id;


--
-- Name: live; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.live (
    id integer NOT NULL,
    council_id integer,
    current_agenda integer
);


ALTER TABLE public.live OWNER TO councilbox;

--
-- Name: live_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.live_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.live_id_seq OWNER TO councilbox;

--
-- Name: live_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.live_id_seq OWNED BY public.live.id;


--
-- Name: live_participants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.live_participants_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.live_participants_id_seq OWNER TO councilbox;

--
-- Name: live_participants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.live_participants_id_seq OWNED BY public.live_participants.id;


--
-- Name: live_participants_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.live_participants_logs (
    id integer NOT NULL,
    user_id integer,
    participant_id integer,
    text character varying,
    date timestamp with time zone DEFAULT now(),
    current_state integer,
    new_state integer,
    current_delegate_id integer,
    new_delegate_id integer,
    representative_deleted_name character varying,
    representative_deleted_surname character varying,
    representative_deleted_dni character varying,
    representative_deleted_email character varying,
    representative_deleted_phone character varying,
    council_id integer,
    type integer DEFAULT 0
);


ALTER TABLE public.live_participants_logs OWNER TO councilbox;

--
-- Name: live_participants_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.live_participants_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.live_participants_logs_id_seq OWNER TO councilbox;

--
-- Name: live_participants_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.live_participants_logs_id_seq OWNED BY public.live_participants_logs.id;


--
-- Name: live_participants_signature_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.live_participants_signature_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.live_participants_signature_id_seq OWNER TO councilbox;

--
-- Name: live_participants_signature_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.live_participants_signature_id_seq OWNED BY public.live_participants_signature.id;


--
-- Name: login_security_rules; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.login_security_rules (
    id integer NOT NULL,
    company_id integer NOT NULL,
    value character varying(255) NOT NULL,
    type character varying(255) DEFAULT 'ALLOW'::character varying NOT NULL
);


ALTER TABLE public.login_security_rules OWNER TO councilbox;

--
-- Name: login_security_rules_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.login_security_rules_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.login_security_rules_id_seq OWNER TO councilbox;

--
-- Name: login_security_rules_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.login_security_rules_id_seq OWNED BY public.login_security_rules.id;


--
-- Name: logs_participants_video; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.logs_participants_video (
    id integer NOT NULL,
    participant_id integer,
    date timestamp with time zone DEFAULT now(),
    type integer DEFAULT 1,
    type_text character varying,
    council_id integer,
    track_info json
);


ALTER TABLE public.logs_participants_video OWNER TO councilbox;

--
-- Name: COLUMN logs_participants_video.type; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.logs_participants_video.type IS '0 --> Disconnection
1 --> Connection';


--
-- Name: COLUMN logs_participants_video.type_text; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.logs_participants_video.type_text IS 'if column type 0 then this column will be ''DISCONNECT'', else if column type 1 then will be ''CONNECT''';


--
-- Name: logs_participants_video_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.logs_participants_video_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.logs_participants_video_id_seq OWNER TO councilbox;

--
-- Name: logs_participants_video_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.logs_participants_video_id_seq OWNED BY public.logs_participants_video.id;


--
-- Name: meetingCelebration; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."meetingCelebration" (
    id integer NOT NULL,
    "meetingId" integer NOT NULL,
    "dateStart" timestamp with time zone,
    "dateEnd" timestamp with time zone,
    "isOpened" boolean DEFAULT false NOT NULL,
    "videoRecording" boolean DEFAULT false NOT NULL
);


ALTER TABLE public."meetingCelebration" OWNER TO councilbox;

--
-- Name: meetingCelebration_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."meetingCelebration_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."meetingCelebration_id_seq" OWNER TO councilbox;

--
-- Name: meetingCelebration_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."meetingCelebration_id_seq" OWNED BY public."meetingCelebration".id;


--
-- Name: meetingConvene; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."meetingConvene" (
    id integer NOT NULL,
    "meetingId" integer NOT NULL,
    "userId" integer,
    "sendDate" timestamp with time zone,
    text character varying(255)
);


ALTER TABLE public."meetingConvene" OWNER TO councilbox;

--
-- Name: meetingConvene_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."meetingConvene_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."meetingConvene_id_seq" OWNER TO councilbox;

--
-- Name: meetingConvene_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."meetingConvene_id_seq" OWNED BY public."meetingConvene".id;


--
-- Name: meetingParticipantConnections; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."meetingParticipantConnections" (
    id integer NOT NULL,
    "meetingId" integer NOT NULL,
    "participantId" integer NOT NULL,
    online public."enum_meetingParticipantConnections_online" DEFAULT 'NO_CONNNECTED'::public."enum_meetingParticipantConnections_online" NOT NULL,
    "refreshDate" timestamp with time zone
);


ALTER TABLE public."meetingParticipantConnections" OWNER TO councilbox;

--
-- Name: meetingParticipantConnections_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."meetingParticipantConnections_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."meetingParticipantConnections_id_seq" OWNER TO councilbox;

--
-- Name: meetingParticipantConnections_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."meetingParticipantConnections_id_seq" OWNED BY public."meetingParticipantConnections".id;


--
-- Name: meetingParticipantSends; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."meetingParticipantSends" (
    id integer NOT NULL,
    "meetingId" integer NOT NULL,
    "participantId" integer NOT NULL,
    "sendDate" timestamp with time zone DEFAULT now(),
    "refreshDate" timestamp with time zone,
    type public."enum_meetingParticipantSends_type" DEFAULT 'CONVENE'::public."enum_meetingParticipantSends_type" NOT NULL,
    code character varying(255),
    state public."enum_meetingParticipantSends_state" DEFAULT 'PENDING_SHIPPING'::public."enum_meetingParticipantSends_state",
    "impositionDate" timestamp with time zone,
    email character varying(255)
);


ALTER TABLE public."meetingParticipantSends" OWNER TO councilbox;

--
-- Name: meetingParticipantSends_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."meetingParticipantSends_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."meetingParticipantSends_id_seq" OWNER TO councilbox;

--
-- Name: meetingParticipantSends_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."meetingParticipantSends_id_seq" OWNED BY public."meetingParticipantSends".id;


--
-- Name: meetingParticipants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."meetingParticipants" (
    "meetingId" integer NOT NULL,
    id integer NOT NULL,
    language character varying(255) DEFAULT 'es'::character varying NOT NULL,
    name character varying(255) NOT NULL,
    surname character varying(255) NOT NULL,
    phone character varying(255),
    uuid uuid NOT NULL,
    password character varying(255) NOT NULL
);


ALTER TABLE public."meetingParticipants" OWNER TO councilbox;

--
-- Name: meetingParticipants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."meetingParticipants_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."meetingParticipants_id_seq" OWNER TO councilbox;

--
-- Name: meetingParticipants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."meetingParticipants_id_seq" OWNED BY public."meetingParticipants".id;


--
-- Name: meetingPlatform; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."meetingPlatform" (
    id integer NOT NULL,
    "meetingId" integer NOT NULL,
    email public."enum_meetingPlatform_email" DEFAULT 'HORUS'::public."enum_meetingPlatform_email" NOT NULL,
    sms public."enum_meetingPlatform_sms" DEFAULT 'TWILIO'::public."enum_meetingPlatform_sms" NOT NULL,
    video public."enum_meetingPlatform_video" DEFAULT 'CMP'::public."enum_meetingPlatform_video" NOT NULL
);


ALTER TABLE public."meetingPlatform" OWNER TO councilbox;

--
-- Name: meetingPlatform_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."meetingPlatform_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."meetingPlatform_id_seq" OWNER TO councilbox;

--
-- Name: meetingPlatform_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."meetingPlatform_id_seq" OWNED BY public."meetingPlatform".id;


--
-- Name: meetings; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.meetings (
    id integer NOT NULL,
    "companyId" integer NOT NULL,
    title character varying(255),
    description text,
    date timestamp with time zone NOT NULL,
    state public.enum_meetings_state DEFAULT 'SAVED'::public.enum_meetings_state NOT NULL,
    "videoRecording" boolean DEFAULT false NOT NULL,
    active boolean DEFAULT true NOT NULL,
    language character varying DEFAULT 'es'::character varying NOT NULL
);


ALTER TABLE public.meetings OWNER TO councilbox;

--
-- Name: meetings_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.meetings_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.meetings_id_seq OWNER TO councilbox;

--
-- Name: meetings_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.meetings_id_seq OWNED BY public.meetings.id;


--
-- Name: modal_writing_act; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.modal_writing_act AS
 SELECT p.id,
    p.council_id,
    (((p.name)::text || ' '::text) || (p.surname)::text) AS name,
    p.dni,
    p.type,
    p.email,
    p.phone,
    p."position",
    p.num_participations,
    p.social_capital
   FROM public.council_participants p
  WHERE (p.actived = 1);


ALTER TABLE public.modal_writing_act OWNER TO councilbox;

--
-- Name: new_platforms; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.new_platforms (
    id integer NOT NULL,
    room integer DEFAULT 1 NOT NULL,
    room_access integer DEFAULT 5 NOT NULL,
    security_email integer DEFAULT 5 NOT NULL,
    security_sms integer DEFAULT 4 NOT NULL,
    video integer DEFAULT 1 NOT NULL,
    company_id integer NOT NULL,
    emails integer DEFAULT 5,
    act integer DEFAULT 5,
    signature integer DEFAULT 8,
    video_config json,
    type character varying(20) DEFAULT NULL::character varying
);


ALTER TABLE public.new_platforms OWNER TO councilbox;

--
-- Name: new_platforms_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.new_platforms_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.new_platforms_id_seq OWNER TO councilbox;

--
-- Name: new_platforms_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.new_platforms_id_seq OWNED BY public.new_platforms.id;


--
-- Name: organizations; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.organizations (
    id integer NOT NULL,
    "creatorId" integer NOT NULL,
    name character varying(255) NOT NULL,
    "creationDate" timestamp with time zone,
    parent integer,
    status integer
);


ALTER TABLE public.organizations OWNER TO councilbox;

--
-- Name: organizations_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.organizations_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.organizations_id_seq OWNER TO councilbox;

--
-- Name: organizations_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.organizations_id_seq OWNED BY public.organizations.id;


--
-- Name: participantSms; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."participantSms" (
    id integer NOT NULL,
    "councilId" integer,
    "liveParticipantId" integer,
    "sendDate" timestamp with time zone DEFAULT now(),
    successfull boolean DEFAULT false NOT NULL,
    phone character varying(255),
    sid character varying(255),
    participant_id integer,
    status integer DEFAULT 22
);


ALTER TABLE public."participantSms" OWNER TO councilbox;

--
-- Name: participantSms_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."participantSms_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."participantSms_id_seq" OWNER TO councilbox;

--
-- Name: participantSms_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."participantSms_id_seq" OWNED BY public."participantSms".id;


--
-- Name: participant_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.participant_logs (
    id integer NOT NULL,
    participant_id integer,
    date timestamp with time zone DEFAULT now(),
    council_id integer,
    name character varying,
    "position" character varying,
    email character varying,
    phone character varying,
    dni character varying,
    num_participations integer,
    surname character varying,
    real_position integer,
    language character varying,
    social_capital integer,
    type integer,
    delegate_id integer
);


ALTER TABLE public.participant_logs OWNER TO councilbox;

--
-- Name: participant_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.participant_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.participant_logs_id_seq OWNER TO councilbox;

--
-- Name: participant_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.participant_logs_id_seq OWNED BY public.participant_logs.id;


--
-- Name: participant_representatives; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.participant_representatives (
    id integer NOT NULL,
    representative_id integer,
    participant_id integer,
    actived integer DEFAULT 0
);


ALTER TABLE public.participant_representatives OWNER TO councilbox;

--
-- Name: participant_representatives_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.participant_representatives_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.participant_representatives_id_seq OWNER TO councilbox;

--
-- Name: participant_representatives_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.participant_representatives_id_seq OWNED BY public.participant_representatives.id;


--
-- Name: participant_surveys; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.participant_surveys (
    id integer NOT NULL,
    council_id integer,
    creation_date timestamp with time zone DEFAULT now(),
    participant_id integer,
    value json
);


ALTER TABLE public.participant_surveys OWNER TO councilbox;

--
-- Name: participant_surveys_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.participant_surveys_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.participant_surveys_id_seq OWNER TO councilbox;

--
-- Name: participant_surveys_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.participant_surveys_id_seq OWNED BY public.participant_surveys.id;


--
-- Name: participants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.participants_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.participants_id_seq OWNER TO councilbox;

--
-- Name: participants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.participants_id_seq OWNED BY public.council_participants.id;


--
-- Name: pdfs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.pdfs (
    id integer NOT NULL,
    filename character varying,
    data character varying,
    council_id integer
);


ALTER TABLE public.pdfs OWNER TO councilbox;

--
-- Name: TABLE pdfs; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON TABLE public.pdfs IS 'Guarda los pdfs generados';


--
-- Name: pdfs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.pdfs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.pdfs_id_seq OWNER TO councilbox;

--
-- Name: pdfs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.pdfs_id_seq OWNED BY public.pdfs.id;


--
-- Name: platforms; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.platforms (
    id integer NOT NULL,
    name character varying,
    code integer
);


ALTER TABLE public.platforms OWNER TO councilbox;

--
-- Name: platforms_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.platforms_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.platforms_id_seq OWNER TO councilbox;

--
-- Name: platforms_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.platforms_id_seq OWNED BY public.platforms.id;


--
-- Name: poll_question_options; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.poll_question_options (
    id integer NOT NULL,
    council_id integer,
    question_id integer,
    title character varying,
    description character varying,
    agenda_id integer
);


ALTER TABLE public.poll_question_options OWNER TO councilbox;

--
-- Name: poll_question_options_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.poll_question_options_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.poll_question_options_id_seq OWNER TO councilbox;

--
-- Name: poll_question_options_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.poll_question_options_id_seq OWNED BY public.poll_question_options.id;


--
-- Name: poll_question_votings; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.poll_question_votings (
    id integer NOT NULL,
    council_id integer NOT NULL,
    question_id integer,
    option_id integer,
    participant_id integer,
    date timestamp with time zone DEFAULT now()
);


ALTER TABLE public.poll_question_votings OWNER TO councilbox;

--
-- Name: poll_question_options_recount; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.poll_question_options_recount AS
 SELECT o.id,
    o.council_id,
    o.question_id,
    o.title,
    ( SELECT count(v.id) AS count
           FROM public.poll_question_votings v
          WHERE (v.option_id = o.id)
          GROUP BY v.option_id) AS total
   FROM public.poll_question_options o;


ALTER TABLE public.poll_question_options_recount OWNER TO councilbox;

--
-- Name: poll_question_votings_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.poll_question_votings_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.poll_question_votings_id_seq OWNER TO councilbox;

--
-- Name: poll_question_votings_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.poll_question_votings_id_seq OWNED BY public.poll_question_votings.id;


--
-- Name: poll_questions; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.poll_questions (
    id integer NOT NULL,
    council_id integer,
    title character varying,
    num_selectable_options integer DEFAULT 1 NOT NULL,
    is_multiselect boolean DEFAULT false,
    is_nominal boolean DEFAULT false,
    write_in boolean DEFAULT false,
    agenda_id integer,
    min_selections integer DEFAULT 1 NOT NULL
);


ALTER TABLE public.poll_questions OWNER TO councilbox;

--
-- Name: poll_questions_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.poll_questions_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.poll_questions_id_seq OWNER TO councilbox;

--
-- Name: poll_questions_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.poll_questions_id_seq OWNED BY public.poll_questions.id;


--
-- Name: poll_questions_options; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.poll_questions_options (
    id integer NOT NULL,
    title character varying(255) DEFAULT 1 NOT NULL,
    agenda_id integer NOT NULL
);


ALTER TABLE public.poll_questions_options OWNER TO councilbox;

--
-- Name: poll_questions_options_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.poll_questions_options_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.poll_questions_options_id_seq OWNER TO councilbox;

--
-- Name: poll_questions_options_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.poll_questions_options_id_seq OWNED BY public.poll_questions_options.id;


--
-- Name: portal_request_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.portal_request_attachments (
    id integer NOT NULL,
    request_id integer NOT NULL,
    filename character varying(255) NOT NULL,
    request_field character varying(255),
    filesize integer,
    filetype character varying(255) NOT NULL
);


ALTER TABLE public.portal_request_attachments OWNER TO councilbox;

--
-- Name: portal_request_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.portal_request_attachments_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.portal_request_attachments_id_seq OWNER TO councilbox;

--
-- Name: portal_request_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.portal_request_attachments_id_seq OWNED BY public.portal_request_attachments.id;


--
-- Name: provinces; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.provinces (
    id integer NOT NULL,
    deno character varying,
    id_province integer,
    country_id integer
);


ALTER TABLE public.provinces OWNER TO councilbox;

--
-- Name: provinces_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.provinces_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.provinces_id_seq OWNER TO councilbox;

--
-- Name: provinces_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.provinces_id_seq OWNED BY public.provinces.id;


--
-- Name: proxy_votes; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.proxy_votes (
    id integer NOT NULL,
    participant_id integer,
    value integer DEFAULT 0,
    agenda_id integer,
    representative_id integer,
    date timestamp with time zone,
    num_participations integer,
    type integer DEFAULT 0
);


ALTER TABLE public.proxy_votes OWNER TO councilbox;

--
-- Name: proxy_votes_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.proxy_votes_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.proxy_votes_id_seq OWNER TO councilbox;

--
-- Name: proxy_votes_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.proxy_votes_id_seq OWNED BY public.proxy_votes.id;


--
-- Name: recording_signatures; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.recording_signatures (
    id integer NOT NULL,
    date timestamp with time zone DEFAULT now(),
    hash text,
    hash_type character varying(255),
    signed_by integer,
    creator_id integer,
    state integer DEFAULT 0 NOT NULL,
    council_id integer NOT NULL,
    recording_id character varying(255) NOT NULL,
    signature text,
    data json
);


ALTER TABLE public.recording_signatures OWNER TO councilbox;

--
-- Name: recording_signatures_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.recording_signatures_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.recording_signatures_id_seq OWNER TO councilbox;

--
-- Name: recording_signatures_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.recording_signatures_id_seq OWNED BY public.recording_signatures.id;


--
-- Name: signature_participants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.signature_participants (
    id integer NOT NULL,
    signature_id integer,
    name character varying(100) NOT NULL,
    surname character varying(100) NOT NULL,
    "position" character varying(100) NOT NULL,
    email character varying(200) NOT NULL,
    phone character varying(100),
    dni character varying(100) NOT NULL,
    platform_code character varying(200),
    platform_req_code character varying(200),
    platform_req_text character varying(200),
    uuid uuid DEFAULT public.uuid_generate_v1(),
    real_position integer,
    language character varying(100),
    status integer,
    request_id text,
    index integer
);


ALTER TABLE public.signature_participants OWNER TO councilbox;

--
-- Name: recount_signatures; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.recount_signatures AS
 SELECT r.signature_id,
    COALESCE(r.required, (0)::bigint) AS required,
    COALESCE(r.finalized, (0)::bigint) AS finalized,
    COALESCE(r.not_signed, (0)::bigint) AS not_signed
   FROM ( SELECT l.signature_id,
            count(l.id) AS required,
            ( SELECT count(lr.id) AS count
                   FROM public.signature_participants lr
                  WHERE ((lr.status = 15) AND (lr.signature_id = l.signature_id))) AS finalized,
            ( SELECT count(lr.id) AS count
                   FROM public.signature_participants lr
                  WHERE ((lr.status <> 15) AND (lr.signature_id = l.signature_id))) AS not_signed
           FROM public.signature_participants l
          GROUP BY l.signature_id
          ORDER BY l.signature_id) r;


ALTER TABLE public.recount_signatures OWNER TO councilbox;

--
-- Name: room_video_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.room_video_logs (
    id integer NOT NULL,
    council_id integer,
    date timestamp with time zone DEFAULT now(),
    type integer,
    type_text character varying,
    track_info json
);


ALTER TABLE public.room_video_logs OWNER TO councilbox;

--
-- Name: room_video_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.room_video_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.room_video_logs_id_seq OWNER TO councilbox;

--
-- Name: room_video_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.room_video_logs_id_seq OWNED BY public.room_video_logs.id;


--
-- Name: root_statistics; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.root_statistics AS
 SELECT countstable.month_name,
    countstable.month_number,
    countstable.year_number,
    sum(countstable.total_companies) AS total_companies,
    sum(countstable.num_of_new_companies_created) AS num_of_new_companies_created,
    sum(countstable.active_companies) AS active_companies,
    sum(countstable.unsubscribes) AS unsubscribes,
    sum(countstable.num_of_new_censuses) AS num_of_new_censuses,
    sum(countstable.total_censuses) AS total_censuses,
    sum(countstable.num_of_new_census_participants) AS num_of_new_census_participants,
    sum(countstable.total_participants_censuses) AS total_participants_censuses,
    sum(countstable.num_of_new_convened_councils) AS num_of_new_convened_councils,
    sum(countstable.total_convened_councils) AS total_convened_councils,
    sum(countstable.num_of_convened_participants) AS num_of_convened_participants,
    sum(countstable.total_convened_participants) AS total_convened_participants,
    sum(countstable.num_of_new_celebrated_councils) AS num_of_new_celebrated_councils,
    sum(countstable.total_celebrated) AS total_celebrated,
    sum(countstable.num_of_new_live_participants) AS num_of_new_live_participants,
    sum(countstable.total_live_participants) AS total_live_participants
   FROM ( SELECT to_char(companies.creation_date, 'TMMonth'::text) AS month_name,
            date_part('month'::text, companies.creation_date) AS month_number,
            date_part('year'::text, companies.creation_date) AS year_number,
            (count(companies.id) + public.gettotalcompanies((date_part('month'::text, companies.creation_date))::integer, (date_part('year'::text, companies.creation_date))::integer)) AS total_companies,
            count(companies.id) AS num_of_new_companies_created,
            0 AS active_companies,
            0 AS unsubscribes,
            0 AS num_of_new_censuses,
            0 AS total_censuses,
            0 AS num_of_new_census_participants,
            0 AS total_participants_censuses,
            0 AS num_of_new_convened_councils,
            0 AS total_convened_councils,
            0 AS num_of_convened_participants,
            0 AS total_convened_participants,
            0 AS num_of_new_celebrated_councils,
            0 AS total_celebrated,
            0 AS num_of_new_live_participants,
            0 AS total_live_participants
           FROM public.companies
          WHERE (companies.demo = 0)
          GROUP BY (to_char(companies.creation_date, 'TMMonth'::text)), (date_part('month'::text, companies.creation_date)), (date_part('year'::text, companies.creation_date))
        UNION
         SELECT to_char(councils.date_start, 'TMMonth'::text) AS month_name,
            date_part('month'::text, councils.date_start) AS month_number,
            date_part('year'::text, councils.date_start) AS year_number,
            0 AS total_companies,
            0 AS num_of_new_companies_created,
            count(DISTINCT companies.id) AS active_companies,
            0 AS unsubscribes,
            0 AS num_of_new_censuses,
            0 AS total_censuses,
            0 AS num_of_new_census_participants,
            0 AS total_participants_censuses,
            0 AS num_of_new_convened_councils,
            0 AS total_convened_councils,
            0 AS num_of_convened_participants,
            0 AS total_convened_participants,
            0 AS num_of_new_celebrated_councils,
            0 AS total_celebrated,
            0 AS num_of_new_live_participants,
            0 AS total_live_participants
           FROM (public.councils
             JOIN public.companies ON ((councils.company_id = companies.id)))
          WHERE (companies.demo = 0)
          GROUP BY (to_char(councils.date_start, 'TMMonth'::text)), (date_part('month'::text, councils.date_start)), (date_part('year'::text, councils.date_start))
        UNION
         SELECT to_char(company_census.creation_date, 'TMMonth'::text) AS month_name,
            date_part('month'::text, company_census.creation_date) AS month_number,
            date_part('year'::text, company_census.creation_date) AS year_number,
            0 AS total_companies,
            0 AS num_of_new_companies_created,
            0 AS active_companies,
            0 AS unsubscribes,
            count(company_census.id) AS num_of_new_censuses,
            (count(company_census.id) + public.gettotalcensuses((date_part('month'::text, company_census.creation_date))::integer, (date_part('year'::text, company_census.creation_date))::integer)) AS total_censuses,
            0 AS num_of_new_census_participants,
            0 AS total_participants_censuses,
            0 AS num_of_new_convened_councils,
            0 AS total_convened_councils,
            0 AS num_of_convened_participants,
            0 AS total_convened_participants,
            0 AS num_of_new_celebrated_councils,
            0 AS total_celebrated,
            0 AS num_of_new_live_participants,
            0 AS total_live_participants
           FROM (public.company_census
             JOIN public.companies ON ((company_census.company_id = companies.id)))
          WHERE (companies.demo = 0)
          GROUP BY (to_char(company_census.creation_date, 'TMMonth'::text)), (date_part('month'::text, company_census.creation_date)), (date_part('year'::text, company_census.creation_date))
        UNION
         SELECT to_char(c.creation_date, 'TMMonth'::text) AS month_name,
            date_part('month'::text, c.creation_date) AS month_number,
            date_part('year'::text, c.creation_date) AS year_number,
            0 AS total_companies,
            0 AS num_of_new_companies_created,
            0 AS active_companies,
            0 AS unsubscribes,
            0 AS num_of_new_censuses,
            0 AS total_censuses,
            count(p.id) AS num_of_new_census_participants,
            (count(p.id) + public.gettotalparticipantscensuses((date_part('month'::text, c.creation_date))::integer, (date_part('year'::text, c.creation_date))::integer)) AS total_participants_censuses,
            0 AS num_of_new_convened_councils,
            0 AS total_convened_councils,
            0 AS num_of_convened_participants,
            0 AS total_convened_participants,
            0 AS num_of_new_celebrated_councils,
            0 AS total_celebrated,
            0 AS num_of_new_live_participants,
            0 AS total_live_participants
           FROM ((public.company_census c
             JOIN public.census_participants p ON ((c.id = p.census_id)))
             JOIN public.companies ON ((c.company_id = companies.id)))
          WHERE (companies.demo = 0)
          GROUP BY (to_char(c.creation_date, 'TMMonth'::text)), (date_part('month'::text, c.creation_date)), (date_part('year'::text, c.creation_date))
        UNION
         SELECT to_char(councils.date_start, 'TMMonth'::text) AS month_name,
            date_part('month'::text, councils.date_start) AS month_number,
            date_part('year'::text, councils.date_start) AS year_number,
            0 AS total_companies,
            0 AS num_of_new_companies_created,
            0 AS active_companies,
            0 AS unsubscribes,
            0 AS num_of_new_censuses,
            0 AS total_censuses,
            0 AS num_of_new_census_participants,
            0 AS total_participants_censuses,
            count(councils.id) AS num_of_new_convened_councils,
            (count(councils.id) + public.gettotalconvenedcouncils((date_part('month'::text, councils.date_start))::integer, (date_part('year'::text, councils.date_start))::integer)) AS total_convened_councils,
            0 AS num_of_convened_participants,
            0 AS total_convened_participants,
            0 AS num_of_new_celebrated_councils,
            0 AS total_celebrated,
            0 AS num_of_new_live_participants,
            0 AS total_live_participants
           FROM (public.councils
             JOIN public.companies ON ((councils.company_id = companies.id)))
          WHERE (companies.demo = 0)
          GROUP BY (to_char(councils.date_start, 'TMMonth'::text)), (date_part('month'::text, councils.date_start)), (date_part('year'::text, councils.date_start))
        UNION
         SELECT to_char(c.date_start, 'TMMonth'::text) AS month_name,
            date_part('month'::text, c.date_start) AS month_number,
            date_part('year'::text, c.date_start) AS year_number,
            0 AS total_companies,
            0 AS num_of_new_companies_created,
            0 AS active_companies,
            0 AS unsubscribes,
            0 AS num_of_new_censuses,
            0 AS total_censuses,
            0 AS num_of_new_census_participants,
            0 AS total_participants_censuses,
            0 AS num_of_new_convened_councils,
            0 AS total_convened_councils,
            count(p.id) AS num_of_convened_participants,
            (count(p.id) + public.gettotalconvenedparticipants((date_part('month'::text, c.date_start))::integer, (date_part('year'::text, c.date_start))::integer)) AS total_convened_participants,
            0 AS num_of_new_celebrated_councils,
            0 AS total_celebrated,
            0 AS num_of_new_live_participants,
            0 AS total_live_participants
           FROM ((public.councils c
             JOIN public.council_participants p ON ((c.id = p.council_id)))
             JOIN public.companies ON ((c.company_id = companies.id)))
          WHERE (companies.demo = 0)
          GROUP BY (to_char(c.date_start, 'TMMonth'::text)), (date_part('month'::text, c.date_start)), (date_part('year'::text, c.date_start))
        UNION
         SELECT to_char(c.date_real_start, 'TMMonth'::text) AS month_name,
            date_part('month'::text, c.date_real_start) AS month_number,
            date_part('year'::text, c.date_real_start) AS year_number,
            0 AS total_companies,
            0 AS num_of_new_companies_created,
            0 AS active_companies,
            0 AS unsubscribes,
            0 AS num_of_new_censuses,
            0 AS total_censuses,
            0 AS num_of_new_census_participants,
            0 AS total_participants_censuses,
            0 AS num_of_new_convened_councils,
            0 AS total_convened_councils,
            0 AS num_of_convened_participants,
            0 AS total_convened_participants,
            count(c.id) AS num_of_new_celebrated_councils,
            (count(c.id) + public.gettotalcelebratedcouncils((date_part('month'::text, c.date_real_start))::integer, (date_part('year'::text, c.date_real_start))::integer)) AS total_celebrated,
            0 AS num_of_new_live_participants,
            0 AS total_live_participants
           FROM (public.councils c
             JOIN public.companies ON ((c.company_id = companies.id)))
          WHERE (companies.demo = 0)
          GROUP BY (to_char(c.date_real_start, 'TMMonth'::text)), (date_part('month'::text, c.date_real_start)), (date_part('year'::text, c.date_real_start))
        UNION
         SELECT to_char(c.date_real_start, 'TMMonth'::text) AS month_name,
            date_part('month'::text, c.date_real_start) AS month_number,
            date_part('year'::text, c.date_real_start) AS year_number,
            0 AS total_companies,
            0 AS num_of_new_companies_created,
            0 AS active_companies,
            0 AS unsubscribes,
            0 AS num_of_new_censuses,
            0 AS total_censuses,
            0 AS num_of_new_census_participants,
            0 AS total_participants_censuses,
            0 AS num_of_new_convened_councils,
            0 AS total_convened_councils,
            0 AS num_of_convened_participants,
            0 AS total_convened_participants,
            0 AS num_of_new_celebrated_councils,
            0 AS total_celebrated,
            count(p.id) AS num_of_new_live_participants,
            (count(p.id) + public.gettotalconvenedparticipantsincelebratedcouncils((date_part('month'::text, c.date_real_start))::integer, (date_part('year'::text, c.date_real_start))::integer)) AS total_live_participants
           FROM ((public.councils c
             JOIN public.council_participants p ON ((c.id = p.council_id)))
             JOIN public.companies ON ((c.company_id = companies.id)))
          WHERE (companies.demo = 0)
          GROUP BY (to_char(c.date_real_start, 'TMMonth'::text)), (date_part('month'::text, c.date_real_start)), (date_part('year'::text, c.date_real_start))) countstable
  WHERE (countstable.year_number > (2016)::double precision)
  GROUP BY countstable.month_name, countstable.month_number, countstable.year_number
  ORDER BY countstable.year_number, countstable.month_number;


ALTER TABLE public.root_statistics OWNER TO councilbox;

--
-- Name: root_statistics_simplified; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.root_statistics_simplified AS
 SELECT root_statistics.month_name AS "Mes",
    root_statistics.month_number,
    root_statistics.year_number,
    root_statistics.total_companies AS "Número de clientes totales",
    root_statistics.num_of_new_companies_created AS "Número de clientes nuevos",
    root_statistics.active_companies AS "Número de clientes activos (alguna operación realizada)",
    root_statistics.unsubscribes AS "Número de clientes que se dan de baja",
    root_statistics.total_censuses AS "Número de censos en el total de clientes",
    root_statistics.total_participants_censuses AS "Número de personas en censos en el total de clientes",
    root_statistics.num_of_new_convened_councils AS "Número de nuevas juntas convocadas",
    root_statistics.total_convened_councils AS "Número total de juntas convocadas",
    root_statistics.total_convened_participants AS "Número de convocados en juntas convocadas",
    root_statistics.num_of_new_celebrated_councils AS "Número de nuevas juntas celebradas",
    root_statistics.total_celebrated AS "Número total de juntas celebradas",
    root_statistics.total_live_participants AS "Número de convocados en juntas celebradas"
   FROM public.root_statistics;


ALTER TABLE public.root_statistics_simplified OWNER TO councilbox;

--
-- Name: scheduled_tasks; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.scheduled_tasks (
    id integer NOT NULL,
    data json,
    council_id integer,
    activation_code character varying(255),
    creation_date timestamp with time zone,
    scheduled_date timestamp with time zone,
    user_id integer,
    type character varying(255),
    state integer DEFAULT 0,
    task_id uuid
);


ALTER TABLE public.scheduled_tasks OWNER TO councilbox;

--
-- Name: scheduled_tasks_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.scheduled_tasks_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.scheduled_tasks_id_seq OWNER TO councilbox;

--
-- Name: scheduled_tasks_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.scheduled_tasks_id_seq OWNED BY public.scheduled_tasks.id;


--
-- Name: schemas; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.schemas (
    id uuid NOT NULL,
    name character varying(255),
    content text NOT NULL,
    resolved_by character varying(255) NOT NULL,
    version character varying(255)
);


ALTER TABLE public.schemas OWNER TO councilbox;

--
-- Name: send_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.send_attachments (
    id integer NOT NULL,
    send_id integer,
    filename character varying(255),
    base64 character varying(255),
    filesize character varying(255),
    filetype character varying(255),
    state integer DEFAULT 0,
    creation_date timestamp with time zone DEFAULT now(),
    document_id integer
);


ALTER TABLE public.send_attachments OWNER TO councilbox;

--
-- Name: send_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.send_attachments_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.send_attachments_id_seq OWNER TO councilbox;

--
-- Name: send_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.send_attachments_id_seq OWNED BY public.send_attachments.id;


--
-- Name: sends_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.sends_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.sends_id_seq OWNER TO councilbox;

--
-- Name: sends_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.sends_id_seq OWNED BY public.sends.id;


--
-- Name: shareholder_request; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.shareholder_request (
    council_id integer NOT NULL,
    id integer NOT NULL,
    data json NOT NULL,
    state character varying(255) NOT NULL,
    date timestamp with time zone DEFAULT now(),
    attachments json,
    participant_created boolean DEFAULT false,
    participant_id integer
);


ALTER TABLE public.shareholder_request OWNER TO councilbox;

--
-- Name: shareholder_request_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.shareholder_request_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.shareholder_request_id_seq OWNER TO councilbox;

--
-- Name: shareholder_request_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.shareholder_request_id_seq OWNED BY public.shareholder_request.id;


--
-- Name: signature_attachments; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.signature_attachments (
    id integer NOT NULL,
    signature_id integer NOT NULL,
    title text,
    description text,
    filename character varying(100) NOT NULL,
    base64 text,
    filesize character varying(100),
    filetype character varying(100)
);


ALTER TABLE public.signature_attachments OWNER TO councilbox;

--
-- Name: signature_attachments_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.signature_attachments_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.signature_attachments_id_seq OWNER TO councilbox;

--
-- Name: signature_attachments_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.signature_attachments_id_seq OWNED BY public.signature_attachments.id;


--
-- Name: signature_participants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.signature_participants_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.signature_participants_id_seq OWNER TO councilbox;

--
-- Name: signature_participants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.signature_participants_id_seq OWNED BY public.signature_participants.id;


--
-- Name: signature_platforms; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.signature_platforms (
    id integer NOT NULL,
    signature_id integer,
    company_id integer,
    signature integer DEFAULT 7
);


ALTER TABLE public.signature_platforms OWNER TO councilbox;

--
-- Name: signature_platforms_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.signature_platforms_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.signature_platforms_id_seq OWNER TO councilbox;

--
-- Name: signature_platforms_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.signature_platforms_id_seq OWNED BY public.signature_platforms.id;


--
-- Name: signatures_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.signatures_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.signatures_id_seq OWNER TO councilbox;

--
-- Name: signatures_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.signatures_id_seq OWNED BY public.signatures.id;


--
-- Name: simple_book_participant_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.simple_book_participant_logs (
    id integer NOT NULL,
    column_name character varying,
    new character varying,
    old character varying,
    date timestamp with time zone DEFAULT now()
);


ALTER TABLE public.simple_book_participant_logs OWNER TO councilbox;

--
-- Name: simple_book_participant_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.simple_book_participant_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.simple_book_participant_logs_id_seq OWNER TO councilbox;

--
-- Name: simple_book_participant_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.simple_book_participant_logs_id_seq OWNED BY public.simple_book_participant_logs.id;


--
-- Name: simple_book_participants; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.simple_book_participants (
    id integer NOT NULL,
    company_id integer,
    name character varying,
    surname character varying DEFAULT ''::character varying,
    dni character varying,
    nationality character varying,
    home character varying,
    language character varying DEFAULT 'es'::character varying,
    real_position integer DEFAULT 1,
    email character varying,
    phone character varying,
    landline_phone character varying,
    type integer DEFAULT 0,
    delegate_id integer,
    uuid uuid DEFAULT public.uuid_generate_v1(),
    delegate_uuid uuid,
    address character varying,
    city character varying,
    country character varying,
    country_state character varying,
    zipcode character varying,
    "position" character varying,
    person_or_entity integer DEFAULT 0,
    state integer DEFAULT 1,
    subscribe_date timestamp with time zone,
    unsubscribe_date timestamp with time zone,
    subscribe_act_date timestamp with time zone,
    unsubscribe_act_date timestamp with time zone,
    open_date timestamp with time zone,
    observations character varying,
    subscribe_act_number character varying,
    unsubscribe_act_number character varying,
    num_participations bigint DEFAULT 1,
    social_capital bigint DEFAULT 1
);


ALTER TABLE public.simple_book_participants OWNER TO councilbox;

--
-- Name: simple_book_participants_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.simple_book_participants_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.simple_book_participants_id_seq OWNER TO councilbox;

--
-- Name: simple_book_participants_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.simple_book_participants_id_seq OWNED BY public.simple_book_participants.id;


--
-- Name: subdomains; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.subdomains (
    id integer NOT NULL,
    name character varying(255) NOT NULL,
    company_id integer,
    background character varying(255),
    "primary" character varying(255),
    title character varying(255),
    secondary character varying(255),
    logo character varying(255),
    icon character varying(255),
    room_background character varying(255),
    hide_signup boolean DEFAULT true,
    styles json DEFAULT '{}'::json,
    contact_email character varying,
    variant character varying(12) DEFAULT 'councilbox'::character varying,
    default_language character varying(3) DEFAULT NULL::character varying,
    appointment_form boolean DEFAULT true,
    client_url character varying,
    kiosk_config json DEFAULT '{}'::json
);


ALTER TABLE public.subdomains OWNER TO councilbox;

--
-- Name: subdomains_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.subdomains_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.subdomains_id_seq OWNER TO councilbox;

--
-- Name: subdomains_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.subdomains_id_seq OWNED BY public.subdomains.id;


--
-- Name: translations; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.translations (
    id integer NOT NULL,
    label character varying,
    es character varying,
    en character varying,
    pt character varying,
    cat character varying,
    gal character varying,
    verified integer DEFAULT 0,
    client_app boolean DEFAULT true
);


ALTER TABLE public.translations OWNER TO councilbox;

--
-- Name: translations2_id_seq1; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.translations2_id_seq1
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.translations2_id_seq1 OWNER TO councilbox;

--
-- Name: translations2_id_seq1; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.translations2_id_seq1 OWNED BY public.translations.id;


--
-- Name: trotter-accounts; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."trotter-accounts" (
    password character varying(255),
    type integer DEFAULT 0,
    name character varying(255),
    surname character varying(255),
    id integer NOT NULL,
    salt character varying(255),
    change_pwd integer DEFAULT 0,
    email character varying(255),
    language character varying(255) DEFAULT 'es'::character varying,
    last_connection_date timestamp with time zone,
    register_date timestamp with time zone DEFAULT now(),
    phone character varying(255),
    organization integer,
    status integer DEFAULT 0
);


ALTER TABLE public."trotter-accounts" OWNER TO councilbox;

--
-- Name: trotter-accounts_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."trotter-accounts_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."trotter-accounts_id_seq" OWNER TO councilbox;

--
-- Name: trotter-accounts_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."trotter-accounts_id_seq" OWNED BY public."trotter-accounts".id;


--
-- Name: trotter-descriptors; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."trotter-descriptors" (
    id integer NOT NULL,
    operation character varying(255) NOT NULL,
    "eventType" character varying(255) NOT NULL,
    value integer,
    fulfilled boolean,
    target character varying(255),
    creator_id integer,
    creation_date timestamp with time zone DEFAULT now()
);


ALTER TABLE public."trotter-descriptors" OWNER TO councilbox;

--
-- Name: trotter-descriptors_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."trotter-descriptors_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."trotter-descriptors_id_seq" OWNER TO councilbox;

--
-- Name: trotter-descriptors_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."trotter-descriptors_id_seq" OWNED BY public."trotter-descriptors".id;


--
-- Name: trotter-evidences; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."trotter-evidences" (
    id integer NOT NULL,
    ev_hash character varying,
    minted boolean DEFAULT false,
    display_name character varying,
    evidence character varying,
    original_evidence character varying,
    creator_id integer,
    register_date timestamp with time zone DEFAULT now(),
    type character varying(50) DEFAULT 'TRACE'::character varying,
    "from" character varying,
    visibility_mask character varying
);


ALTER TABLE public."trotter-evidences" OWNER TO councilbox;

--
-- Name: trotter-evidences_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."trotter-evidences_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."trotter-evidences_id_seq" OWNER TO councilbox;

--
-- Name: trotter-evidences_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."trotter-evidences_id_seq" OWNED BY public."trotter-evidences".id;


--
-- Name: trotter-feature_exceptions; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."trotter-feature_exceptions" (
    id integer NOT NULL,
    feature_name character varying(255),
    company_id integer NOT NULL,
    active boolean DEFAULT false NOT NULL,
    "featureId" integer
);


ALTER TABLE public."trotter-feature_exceptions" OWNER TO councilbox;

--
-- Name: trotter-feature_exceptions_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."trotter-feature_exceptions_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."trotter-feature_exceptions_id_seq" OWNER TO councilbox;

--
-- Name: trotter-feature_exceptions_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."trotter-feature_exceptions_id_seq" OWNED BY public."trotter-feature_exceptions".id;


--
-- Name: trotter-features; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."trotter-features" (
    id integer NOT NULL,
    name character varying(255) NOT NULL,
    active boolean DEFAULT false NOT NULL
);


ALTER TABLE public."trotter-features" OWNER TO councilbox;

--
-- Name: trotter-features_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."trotter-features_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."trotter-features_id_seq" OWNER TO councilbox;

--
-- Name: trotter-features_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."trotter-features_id_seq" OWNED BY public."trotter-features".id;


--
-- Name: trotter-organizations; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."trotter-organizations" (
    id integer NOT NULL,
    "creatorId" integer NOT NULL,
    name character varying(255) NOT NULL,
    "creationDate" timestamp with time zone,
    parent integer,
    status integer DEFAULT 1 NOT NULL
);


ALTER TABLE public."trotter-organizations" OWNER TO councilbox;

--
-- Name: trotter-organizations_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."trotter-organizations_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."trotter-organizations_id_seq" OWNER TO councilbox;

--
-- Name: trotter-organizations_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."trotter-organizations_id_seq" OWNED BY public."trotter-organizations".id;


--
-- Name: trotter-trace_descriptors; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public."trotter-trace_descriptors" (
    id integer NOT NULL,
    trace_id character varying(255) NOT NULL,
    operation character varying(255) NOT NULL,
    "eventType" character varying(255) NOT NULL,
    value integer,
    fulfilled boolean,
    target character varying(255),
    register_date timestamp with time zone DEFAULT now()
);


ALTER TABLE public."trotter-trace_descriptors" OWNER TO councilbox;

--
-- Name: trotter-trace_descriptors_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public."trotter-trace_descriptors_id_seq"
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public."trotter-trace_descriptors_id_seq" OWNER TO councilbox;

--
-- Name: trotter-trace_descriptors_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public."trotter-trace_descriptors_id_seq" OWNED BY public."trotter-trace_descriptors".id;


--
-- Name: user_company; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.user_company (
    id integer NOT NULL,
    user_id integer,
    company_id integer,
    last_date_actived timestamp with time zone,
    actived integer DEFAULT 0,
    role character varying(20) DEFAULT 'admin'::character varying,
    roles character varying,
    main integer DEFAULT 0
);


ALTER TABLE public.user_company OWNER TO councilbox;

--
-- Name: COLUMN user_company.company_id; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.user_company.company_id IS '

';


--
-- Name: user_company_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.user_company_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.user_company_id_seq OWNER TO councilbox;

--
-- Name: user_company_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.user_company_id_seq OWNED BY public.user_company.id;


--
-- Name: user_councils; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.user_councils AS
 SELECT u.user_id,
    c.id AS council_id,
    c.company_id
   FROM (public.user_company u
     LEFT JOIN public.councils c ON ((u.company_id = c.company_id)));


ALTER TABLE public.user_councils OWNER TO councilbox;

--
-- Name: user_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.user_logs (
    id integer NOT NULL,
    user_id integer,
    date timestamp with time zone DEFAULT now(),
    type character varying(255) NOT NULL,
    data json
);


ALTER TABLE public.user_logs OWNER TO councilbox;

--
-- Name: user_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.user_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.user_logs_id_seq OWNER TO councilbox;

--
-- Name: user_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.user_logs_id_seq OWNED BY public.evidence_logs.id;


--
-- Name: user_logs_id_seq1; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.user_logs_id_seq1
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.user_logs_id_seq1 OWNER TO councilbox;

--
-- Name: user_logs_id_seq1; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.user_logs_id_seq1 OWNED BY public.user_logs.id;


--
-- Name: user_schedules; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.user_schedules (
    id integer NOT NULL,
    user_id integer,
    active double precision DEFAULT '1'::double precision,
    time_start double precision NOT NULL,
    time_end integer NOT NULL,
    date_start timestamp with time zone NOT NULL,
    date_end timestamp with time zone NOT NULL,
    created_at timestamp with time zone,
    last_updated timestamp with time zone,
    days text NOT NULL
);


ALTER TABLE public.user_schedules OWNER TO councilbox;

--
-- Name: user_schedules_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.user_schedules_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.user_schedules_id_seq OWNER TO councilbox;

--
-- Name: user_schedules_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.user_schedules_id_seq OWNED BY public.user_schedules.id;


--
-- Name: user_sends; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.user_sends (
    id integer NOT NULL,
    user_id integer,
    send_date timestamp with time zone DEFAULT now(),
    refresh_date timestamp with time zone,
    send_type integer DEFAULT '-1'::integer,
    code character varying,
    req_code integer,
    imposition_date timestamp with time zone,
    email character varying,
    data json
);


ALTER TABLE public.user_sends OWNER TO councilbox;

--
-- Name: user_sends_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.user_sends_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.user_sends_id_seq OWNER TO councilbox;

--
-- Name: user_sends_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.user_sends_id_seq OWNED BY public.user_sends.id;


--
-- Name: user_signatures; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.user_signatures AS
 SELECT u.user_id,
    s.id AS signature_id,
    s.request_id AS signature_request_id,
    s.company_id
   FROM (public.user_company u
     LEFT JOIN public.signatures s ON ((u.company_id = s.company_id)));


ALTER TABLE public.user_signatures OWNER TO councilbox;

--
-- Name: user_solpheouser; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.user_solpheouser (
    id integer NOT NULL,
    user_id integer NOT NULL,
    solpheo_user_id character varying NOT NULL,
    solpheo_user_username character varying,
    solpheo_user_email character varying,
    solpheo_user_token character varying
);


ALTER TABLE public.user_solpheouser OWNER TO councilbox;

--
-- Name: user_solpheouser_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.user_solpheouser_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.user_solpheouser_id_seq OWNER TO councilbox;

--
-- Name: user_solpheouser_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.user_solpheouser_id_seq OWNED BY public.user_solpheouser.id;


--
-- Name: user_video_room; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.user_video_room (
    id integer NOT NULL,
    user_id integer,
    room_id text NOT NULL,
    room_participant_id text NOT NULL,
    council_id integer NOT NULL,
    company_id integer NOT NULL
);


ALTER TABLE public.user_video_room OWNER TO councilbox;

--
-- Name: user_video_room_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.user_video_room_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.user_video_room_id_seq OWNER TO councilbox;

--
-- Name: user_video_room_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.user_video_room_id_seq OWNED BY public.user_video_room.id;


--
-- Name: users; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.users (
    usr character varying,
    pwd character varying,
    profile integer DEFAULT 0,
    name character varying,
    surname character varying,
    roles character varying DEFAULT 'admin'::character varying NOT NULL,
    cfs_user character varying,
    cfs_pass character varying,
    id integer NOT NULL,
    salt character varying,
    change_pwd integer DEFAULT 0,
    email character varying,
    token character varying,
    expire_token character varying,
    preferred_language character varying DEFAULT 'es'::character varying,
    actived integer DEFAULT 0,
    register_token character varying,
    last_connection_date timestamp with time zone,
    register_date timestamp with time zone DEFAULT now(),
    phone character varying,
    code character varying,
    corporation_id integer DEFAULT 1,
    user_type character varying DEFAULT 'company'::character varying,
    apikey_id integer,
    id_card character varying DEFAULT NULL::character varying,
    last_password_update timestamp with time zone,
    security_type integer DEFAULT 0,
    password_reminder_sent boolean DEFAULT false,
    external_id character varying,
    update_date timestamp with time zone
);


ALTER TABLE public.users OWNER TO councilbox;

--
-- Name: COLUMN users.profile; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.users.profile IS '0 --> normal user
1 --> admin';


--
-- Name: users_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.users_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.users_id_seq OWNER TO councilbox;

--
-- Name: users_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.users_id_seq OWNED BY public.users.id;


--
-- Name: v_act_sends; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.v_act_sends AS
 SELECT DISTINCT ON (sends.participant_id) sends.id,
    sends.council_id,
    sends.participant_id,
    sends.send_date,
    sends.refresh_date,
    sends.send_type,
    sends.code,
    sends.req_code,
    sends.imposition_date,
    sends.email
   FROM public.sends
  WHERE (sends.send_type = 6)
  ORDER BY sends.participant_id, sends.send_date DESC;


ALTER TABLE public.v_act_sends OWNER TO councilbox;

--
-- Name: v_convene_sends; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.v_convene_sends AS
 SELECT DISTINCT ON (sends.participant_id) sends.id,
    sends.council_id,
    sends.participant_id,
    sends.send_date,
    sends.refresh_date,
    sends.send_type,
    sends.code,
    sends.req_code,
    sends.imposition_date,
    sends.email
   FROM public.sends
  WHERE ((sends.send_type = 0) OR (sends.send_type = 2))
  ORDER BY sends.participant_id, sends.send_date DESC;


ALTER TABLE public.v_convene_sends OWNER TO councilbox;

--
-- Name: v_council_participants_convene; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.v_council_participants_convene AS
 SELECT p.id,
    p.council_id,
    p.name,
    p.surname,
    p."position",
    p.email,
    p.dni,
    p.phone,
    p.date,
    p.type,
    p.num_participations,
    p.social_capital,
    p.uuid,
    p.video_password,
    p.real_position,
    p.language,
    s.send_id,
    s.code,
    s.req_code,
    s.send_date,
    l.assistance_intention,
    l.assistance_comment,
    l.assistance_last_date_confirmed,
    l.state
   FROM ((public.council_participants p
     LEFT JOIN ( SELECT DISTINCT ON (sends.participant_id) sends.id AS send_id,
            sends.code,
            sends.req_code,
            sends.send_date,
            sends.send_type,
            sends.participant_id
           FROM public.sends
          WHERE ((sends.send_type = 0) OR (sends.send_type = 2))
          ORDER BY sends.participant_id, sends.send_date DESC) s ON ((p.id = s.participant_id)))
     JOIN public.live_participants l ON ((p.id = l.participant_id)));


ALTER TABLE public.v_council_participants_convene OWNER TO councilbox;

--
-- Name: v_credentials_sends; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.v_credentials_sends AS
 SELECT DISTINCT ON (sends.live_participant_id) sends.id,
    sends.council_id,
    sends.participant_id,
    sends.send_date,
    sends.refresh_date,
    sends.send_type,
    sends.code,
    sends.req_code,
    sends.imposition_date,
    sends.email
   FROM public.sends
  WHERE (sends.send_type = 4)
  ORDER BY sends.live_participant_id, sends.send_date DESC;


ALTER TABLE public.v_credentials_sends OWNER TO councilbox;

--
-- Name: v_live_participants_credentials; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.v_live_participants_credentials AS
 SELECT l.id,
    l.council_id,
    l.name,
    l.surname,
    l."position",
    l.email,
    l.dni,
    l.phone,
    l.participant_id,
    l.date,
    l.type,
    l.num_participations,
    l.social_capital,
    l.uuid,
    l.video_password,
    l.assistance_intention,
    l.assistance_comment,
    l.assistance_last_date_confirmed,
    l.state,
    l.signed,
    p.real_position,
    p.language,
    s.send_id,
    s.code,
    s.req_code,
    s.send_date
   FROM ((public.live_participants l
     LEFT JOIN ( SELECT DISTINCT ON (sends.live_participant_id) sends.id AS send_id,
            sends.code,
            sends.req_code,
            sends.send_date,
            sends.send_type,
            sends.live_participant_id
           FROM public.sends
          WHERE (sends.send_type = 4)
          ORDER BY sends.live_participant_id, sends.send_date DESC) s ON ((l.id = s.live_participant_id)))
     LEFT JOIN public.council_participants p ON ((p.id = l.participant_id)));


ALTER TABLE public.v_live_participants_credentials OWNER TO councilbox;

--
-- Name: votings; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.votings (
    id integer NOT NULL,
    agenda_id integer,
    participant_email character varying,
    code integer,
    date timestamp with time zone,
    cfs_code character varying,
    req_code character varying,
    req_text character varying(2044),
    participant_id integer,
    comment text,
    be_on_record integer DEFAULT 1,
    vote integer DEFAULT '-1'::integer,
    council_id integer,
    num_participations bigint DEFAULT 1,
    delegate_id integer,
    cfs_url character varying,
    present_vote integer DEFAULT 0,
    uuid uuid DEFAULT public.uuid_generate_v1(),
    fixed boolean DEFAULT false,
    comment_date timestamp with time zone,
    creation_date timestamp with time zone,
    type integer DEFAULT 0
);


ALTER TABLE public.votings OWNER TO councilbox;

--
-- Name: TABLE votings; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON TABLE public.votings IS 'last_update';


--
-- Name: COLUMN votings.code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings.code IS 'Código que devuelve la plataforma cfs.
Por lo general 2180 es que se ha enviado correctamente.';


--
-- Name: COLUMN votings.cfs_code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings.cfs_code IS 'Código que devuelve la plataforma cfs, el cual usaremos para ver el estado de la votación de ese participante.';


--
-- Name: COLUMN votings.req_code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings.req_code IS 'Resultado de la petición a cfs con el cfs_code, que nos dice como está dicha votación.';


--
-- Name: COLUMN votings.be_on_record; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings.be_on_record IS 'Campo que nos indica si quiere que el comentario conste o no en acta.
0 --> No
1 --> Si';


--
-- Name: COLUMN votings.vote; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings.vote IS 'Guarda el resultado de la votación de una agenda de tipo votación.
0 --> Rechaza
1 --> Acepta';


--
-- Name: vagenda_quality_vote; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vagenda_quality_vote AS
 SELECT a.id,
    c.id AS council_id,
    v.vote
   FROM ((public.agendas a
     LEFT JOIN public.councils c ON ((a.council_id = c.id)))
     LEFT JOIN public.votings v ON (((a.id = v.agenda_id) AND (c.quality_vote_id = v.participant_id))));


ALTER TABLE public.vagenda_quality_vote OWNER TO councilbox;

--
-- Name: vcensus_participants; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcensus_participants AS
 SELECT p.id,
    p.delegate_id,
    p.company_id,
    p.census_id,
    (((p.name)::text || ' '::text) || (p.surname)::text) AS name,
    p.dni,
    p.type,
    p.email,
    p.phone,
    p."position",
    p.num_participations,
    p.social_capital,
    (((r.name)::text || ' '::text) || (r.surname)::text) AS rname,
    r.dni AS rdni,
    r.email AS remail,
    r.phone AS rphone,
    r."position" AS rposition
   FROM (public.census_participants p
     LEFT JOIN public.census_participants r ON ((p.delegate_id = r.id)));


ALTER TABLE public.vcensus_participants OWNER TO councilbox;

--
-- Name: vcensus_users; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcensus_users AS
 SELECT c.id,
    c.company_id,
    c.census_name,
    c.default_census,
    c.census_description,
    c.creator_id,
    c.creation_date,
    c.last_edit,
    c.state,
    c.quorum_prototype,
    u.name AS creator_name,
    u.surname AS creator_surname
   FROM (public.company_census c
     LEFT JOIN public.users u ON ((c.creator_id = u.id)));


ALTER TABLE public.vcensus_users OWNER TO councilbox;

--
-- Name: vcomments; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcomments AS
 SELECT lp.email,
    lp.name,
    lp.surname,
    lp."position",
    comments.id,
    comments.council_id,
    comments.agenda_id,
    comments.participant_id,
    comments.text,
    comments.date
   FROM (public.comments
     LEFT JOIN public.live_participants lp ON ((lp.id = comments.participant_id)));


ALTER TABLE public.vcomments OWNER TO councilbox;

--
-- Name: vcompanies; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcompanies AS
 SELECT c.alias,
    c.tin,
    c.logo,
    c.id,
    c.business_name,
    c.address,
    c.city,
    c.zipcode,
    c.country,
    c.country_state,
    c.link_key,
    c.creator_id,
    uc.user_id,
    c.domain,
    c.council_quorum_percentage,
    c.agreement_quorum_percentage,
    c.advance_notice_days,
    c.demo
   FROM (public.companies c
     LEFT JOIN public.user_company uc ON ((c.id = uc.company_id)));


ALTER TABLE public.vcompanies OWNER TO councilbox;

--
-- Name: vcompanies_light; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcompanies_light AS
 SELECT c.alias,
    c.id,
    c.business_name,
    uc.user_id,
    c.demo
   FROM (public.companies c
     LEFT JOIN public.user_company uc ON ((c.id = uc.company_id)));


ALTER TABLE public.vcompanies_light OWNER TO councilbox;

--
-- Name: vcouncil_attachments; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcouncil_attachments AS
 SELECT attachments.id,
    attachments.agenda_id,
    attachments.filename,
    attachments.filesize,
    attachments.filetype,
    agendas.council_id
   FROM (public.agenda_attachments attachments
     JOIN public.agendas agendas ON ((attachments.agenda_id = agendas.id)));


ALTER TABLE public.vcouncil_attachments OWNER TO councilbox;

--
-- Name: vcouncil_corporates; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcouncil_corporates AS
 SELECT c2.id AS company_id,
    c.company_corporate_id,
    c.council_id,
    c.convene_subject,
    c.convene_body,
    c.meeting_subject,
    c.meeting_body,
    c.reminder_subject,
    c.reminder_body,
    c.reprogrammed_subject,
    c.reprogrammed_body,
    c.canceled_subject,
    c.canceled_body,
    c.room_access_subject,
    c.room_access_body,
    c.meeting_access_subject,
    c.meeting_access_body,
    c.password_email_subject,
    c.password_email_body,
    c.password_sms_body,
    c2.logo
   FROM (public.council_corporates c
     RIGHT JOIN public.companies c2 ON ((c.company_id = c2.id)));


ALTER TABLE public.vcouncil_corporates OWNER TO councilbox;

--
-- Name: vcouncil_participants; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcouncil_participants AS
 SELECT p.id,
    p.delegate_id,
    p.council_id,
    (((p.name)::text || ' '::text) || (p.surname)::text) AS name,
    p.dni,
    p.type,
    p.email,
    p.phone,
    p."position",
    p.num_participations,
    p.social_capital,
    (((r.name)::text || ' '::text) || (r.surname)::text) AS rname,
    r.dni AS rdni,
    r.email AS remail,
    r.phone AS rphone,
    r."position" AS rposition
   FROM (public.council_participants p
     LEFT JOIN public.council_participants r ON ((p.delegate_id = r.id)));


ALTER TABLE public.vcouncil_participants OWNER TO councilbox;

--
-- Name: vcouncil_participants_councils; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcouncil_participants_councils AS
 SELECT councils.id AS council_id,
    council_participants.id AS participant_id
   FROM (public.councils
     JOIN public.council_participants ON ((councils.id = council_participants.council_id)))
  ORDER BY councils.id;


ALTER TABLE public.vcouncil_participants_councils OWNER TO councilbox;

--
-- Name: vcouncil_participants_with_representatives; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcouncil_participants_with_representatives AS
 SELECT p.id,
    p.delegate_id,
    p.council_id,
    (((p.name)::text || ' '::text) || (p.surname)::text) AS name,
    p.dni,
    p.type,
    p.email,
    p.phone,
    p."position",
    p.num_participations,
    p.social_capital,
    l.assistance_intention,
    l.assistance_comment,
    l.assistance_last_date_confirmed,
    l.id AS live_participant_id,
    s.send_id,
    s.code,
    s.req_code,
    s.send_date,
    s.send_type,
    (((r.name)::text || ' '::text) || (r.surname)::text) AS rname,
    r.dni AS rdni,
    r.email AS remail,
    r.phone AS rphone,
    r."position" AS rposition,
    l2.assistance_intention AS rassistance_intention,
    l2.assistance_comment AS rassistance_comment,
    l2.assistance_last_date_confirmed AS rassistance_last_date_confirmed,
    l2.id AS rlive_participant_id,
    s2.send_id AS rsend_id,
    s2.code AS rcode,
    s2.req_code AS rreq_code,
    s2.send_date AS rsend_date,
    s2.send_type AS rsend_type
   FROM (((((public.council_participants p
     JOIN public.live_participants l ON ((p.id = l.participant_id)))
     LEFT JOIN public.council_participants r ON ((p.delegate_id = r.id)))
     LEFT JOIN public.live_participants l2 ON ((p.delegate_id = l2.participant_id)))
     LEFT JOIN ( SELECT DISTINCT ON (sends.participant_id) sends.id AS send_id,
            sends.code,
            sends.req_code,
            sends.send_date,
            sends.send_type,
            sends.participant_id
           FROM public.sends
          WHERE ((sends.send_type = 0) OR (sends.send_type = 2))
          ORDER BY sends.participant_id, sends.send_date DESC) s ON ((p.id = s.participant_id)))
     LEFT JOIN ( SELECT DISTINCT ON (sends.participant_id) sends.id AS send_id,
            sends.code,
            sends.req_code,
            sends.send_date,
            sends.send_type,
            sends.participant_id
           FROM public.sends
          WHERE ((sends.send_type = 0) OR (sends.send_type = 2))
          ORDER BY sends.participant_id, sends.send_date DESC) s2 ON ((p.delegate_id = s2.participant_id)))
  WHERE (p.type <> 2);


ALTER TABLE public.vcouncil_participants_with_representatives OWNER TO councilbox;

--
-- Name: vcouncils; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcouncils AS
 SELECT c.id,
    c.company_id,
    c.business_name,
    c.tin,
    c.name,
    c.date_start,
    c.country,
    c.country_state,
    c.city,
    c.zipcode,
    c.header_logo,
    c.convene_text,
    c.send_date,
    c.state,
    c.date_end,
    c.street,
    c.email_text,
    c.date_real_start,
    c.duration,
    c.current,
    c.email_app,
    c.weighted_voting,
    c.council_type,
    c.security_type,
    c.security_key,
    c.shortname,
    c.president,
    c.secretary,
    c.votation_type,
    c.send_points_mode,
    c.full_video_record,
    c.quorum_prototype,
    c.prototype,
    c.date_start_2nd_call,
    c.prototype_first_call_quorum,
    c.prototype_second_call_quorum,
    c.video_email_template,
    c.security_email_template,
    c.video_emails_date,
    c.approve_act_draft,
    c.confirm_assistance,
    c.selected_census_id,
    c.video_mode,
    c.active,
    c.video_recoding_initialized,
    c.council_started,
    c.date_open_room,
    c.first_or_second_convene,
    c.needed_quorum,
    c.current_quorum,
    c.satisfy_quorum,
    c.no_celebrate_comment,
    c.step,
    ce.included_in_act_book,
    co.corporation_id
   FROM ((public.councils c
     LEFT JOIN public.council_statutes ce ON ((c.id = ce.council_id)))
     LEFT JOIN public.companies co ON ((c.company_id = co.id)));


ALTER TABLE public.vcouncils OWNER TO councilbox;

--
-- Name: COLUMN vcouncils.state; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vcouncils.state IS ' -1  -->   blocked
  0  -->   planned
  1  -->   draft
  2  -->   started
  3  -->   finished';


--
-- Name: COLUMN vcouncils.current; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vcouncils.current IS '0 --> false
1 --> true';


--
-- Name: COLUMN vcouncils.send_points_mode; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vcouncils.send_points_mode IS '0 --> All; 
1 --> Connected;';


--
-- Name: COLUMN vcouncils.full_video_record; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vcouncils.full_video_record IS '0 --> No, no record full council; 
1 --> Yes, record full council;';


--
-- Name: COLUMN vcouncils.prototype; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vcouncils.prototype IS '0 --> General Assembly; 
1 --> Extraordinary Assembly; 
2 --> Board Directors; 
3 --> Meeting';


--
-- Name: vcurrent_liveparticipants; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vcurrent_liveparticipants AS
 SELECT live_participants.council_id,
    live_participants.id,
    (((live_participants.name)::text || ' '::text) || (live_participants.surname)::text) AS name,
    live_participants.dni,
    live_participants."position"
   FROM public.live_participants
  WHERE ((live_participants.blocked <> 1) AND (live_participants.actived = 1) AND ((live_participants.type = 0) OR (live_participants.type = 2)) AND ((live_participants.state = 7) OR (live_participants.state = 5) OR (live_participants.state = 0)));


ALTER TABLE public.vcurrent_liveparticipants OWNER TO councilbox;

--
-- Name: video_captures; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.video_captures (
    id integer NOT NULL,
    participant_id integer NOT NULL,
    date timestamp with time zone DEFAULT now(),
    last_update timestamp with time zone DEFAULT now(),
    name character varying(255),
    council_id integer NOT NULL,
    capture_id character varying(255),
    room_id character varying(255),
    state integer DEFAULT 1
);


ALTER TABLE public.video_captures OWNER TO councilbox;

--
-- Name: video_captures_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.video_captures_id_seq
    AS integer
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.video_captures_id_seq OWNER TO councilbox;

--
-- Name: video_captures_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.video_captures_id_seq OWNED BY public.video_captures.id;


--
-- Name: video_votings_logs; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.video_votings_logs (
    id integer NOT NULL,
    council_id integer,
    agenda_id integer,
    participant_id integer,
    date timestamp with time zone DEFAULT now(),
    current_vote integer,
    new_vote integer,
    ip character varying,
    user_agent character varying,
    current_comment text,
    new_comment text,
    track_info json,
    voting_id integer
);


ALTER TABLE public.video_votings_logs OWNER TO councilbox;

--
-- Name: video_votings_logs_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.video_votings_logs_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.video_votings_logs_id_seq OWNER TO councilbox;

--
-- Name: video_votings_logs_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.video_votings_logs_id_seq OWNED BY public.video_votings_logs.id;


--
-- Name: vlive_participants_connections; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vlive_participants_connections AS
 SELECT DISTINCT ON ((to_char(l.date, 'dd/mm/YYYY,HH24:MI'::text)), p.id, l.type_text) p.name,
    p.surname,
    p.council_id,
    p.id AS live_participant_id,
    p."position",
    p.dni,
    l.type_text,
    l.date
   FROM (public.logs_participants_video l
     JOIN public.live_participants p ON ((l.participant_id = p.id)))
  ORDER BY (to_char(l.date, 'dd/mm/YYYY,HH24:MI'::text)), p.id, l.type_text;


ALTER TABLE public.vlive_participants_connections OWNER TO councilbox;

--
-- Name: vlive_participants_councils; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vlive_participants_councils AS
 SELECT councils.id AS council_id,
    councils.state AS council_state,
    councils.council_started,
    live_participants.id AS participant_id,
    live_participants.uuid,
    live_participants.participant_id AS council_participant_id,
    live_participants.actived
   FROM (public.councils
     JOIN public.live_participants ON ((councils.id = live_participants.council_id)));


ALTER TABLE public.vlive_participants_councils OWNER TO councilbox;

--
-- Name: vlive_participants_logs; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vlive_participants_logs AS
 SELECT l.id,
    l.user_id,
    l.participant_id,
    l.text,
    l.date,
    l.current_state,
    l.new_state,
    l.current_delegate_id,
    l.new_delegate_id,
    l.representative_deleted_name,
    l.representative_deleted_surname,
    l.representative_deleted_dni,
    l.representative_deleted_email,
    l.representative_deleted_phone,
    l.council_id,
    l.type,
    u.usr AS "user",
    u.email,
    lp.name AS current_delegate_name,
    lp."position" AS current_delegate_position,
    lp.email AS current_delegate_email,
    lp.phone AS current_delegate_phone,
    lp.dni AS current_delegate_dni,
    lp2.name AS new_delegate_name,
    lp2."position" AS new_delegate_position,
    lp2.email AS new_delegate_email,
    lp2.phone AS new_delegate_phone,
    lp2.dni AS new_delegate_dni,
    lp2.surname AS new_delegate_surname,
    lp.surname AS current_delegate_surname
   FROM (((public.live_participants_logs l
     LEFT JOIN public.users u ON ((l.user_id = u.id)))
     LEFT JOIN public.live_participants lp ON ((l.current_delegate_id = lp.id)))
     LEFT JOIN public.live_participants lp2 ON ((l.new_delegate_id = lp2.id)));


ALTER TABLE public.vlive_participants_logs OWNER TO councilbox;

--
-- Name: vlive_participants_to_delegate; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vlive_participants_to_delegate AS
 SELECT l.id,
    l.delegate_id,
    l.state,
    l.audio,
    l.video,
    l.council_id,
    (((l.name)::text || ' '::text) || (l.surname)::text) AS name,
    l."position",
    l.email,
    l.phone,
    l.dni,
    l.date,
    l.type,
    l.participant_id,
    l.online,
    l.request_word,
    l.num_participations,
    l.surname,
    l.uuid,
    l.assistance_comment,
    l.assistance_last_date_confirmed,
    l.assistance_intention,
    l.video_password,
    l.blocked,
    l.last_date_connection,
    ( SELECT count(ld.id) AS num_delegated_votes
           FROM public.live_participants ld
          WHERE (l.id = ld.delegate_id)) AS num_delegated_votes,
    l.actived
   FROM public.live_participants l;


ALTER TABLE public.vlive_participants_to_delegate OWNER TO councilbox;

--
-- Name: COLUMN vlive_participants_to_delegate.online; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vlive_participants_to_delegate.online IS '0 --> Nunca conectado 
1 --> Alguna vez conectado 
2 --> Conectado actualmente';


--
-- Name: COLUMN vlive_participants_to_delegate.request_word; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vlive_participants_to_delegate.request_word IS '0-> no request word 
1-> waiting to speak 
2-> speaking';


--
-- Name: vlive_participants_with_representatives; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vlive_participants_with_representatives AS
 SELECT l.id,
    l.council_id,
    (((l.name)::text || ' '::text) || (l.surname)::text) AS name,
    l.surname,
    l."position",
    l.email,
    l.dni,
    l.phone,
    l.participant_id,
    l.date,
    l.type,
    l.num_participations,
    l.social_capital,
    l.uuid,
    l.video_password,
    l.assistance_intention,
    l.assistance_comment,
    l.assistance_last_date_confirmed,
    l.state,
    l.signed,
    l.actived,
    p.real_position,
    p.language,
    s.send_id,
    s.code,
    s.req_code,
    s.send_date,
    (((r.name)::text || ' '::text) || (r.surname)::text) AS rname,
    r.dni AS rdni,
    r.email AS remail,
    r.phone AS rphone,
    r."position" AS rposition,
    r.assistance_intention AS rassistance_intention,
    r.assistance_comment AS rassistance_comment,
    r.assistance_last_date_confirmed AS rassistance_last_date_confirmed,
    s2.send_id AS rsend_id,
    s2.code AS rcode,
    s2.req_code AS rreq_code,
    s2.send_date AS rsend_date,
    s2.send_type AS rsend_type
   FROM ((((public.live_participants l
     LEFT JOIN public.live_participants r ON ((l.delegate_id = r.id)))
     LEFT JOIN ( SELECT DISTINCT ON (sends.live_participant_id) sends.id AS send_id,
            sends.code,
            sends.req_code,
            sends.send_date,
            sends.send_type,
            sends.live_participant_id
           FROM public.sends
          WHERE (sends.send_type = 4)
          ORDER BY sends.live_participant_id, sends.send_date DESC) s ON ((l.id = s.live_participant_id)))
     LEFT JOIN ( SELECT DISTINCT ON (sends.live_participant_id) sends.id AS send_id,
            sends.code,
            sends.req_code,
            sends.send_date,
            sends.send_type,
            sends.live_participant_id
           FROM public.sends
          WHERE (sends.send_type = 4)
          ORDER BY sends.live_participant_id, sends.send_date DESC) s2 ON ((l.delegate_id = s.live_participant_id)))
     LEFT JOIN public.council_participants p ON ((p.id = l.participant_id)));


ALTER TABLE public.vlive_participants_with_representatives OWNER TO councilbox;

--
-- Name: vlogs_participants_video; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vlogs_participants_video AS
 SELECT logs.participant_id,
    p.name,
    p.surname,
    p."position",
    p.dni,
    logs.date,
    logs.type_text,
    logs.council_id
   FROM (public.logs_participants_video logs
     JOIN public.live_participants p ON ((logs.participant_id = p.id)));


ALTER TABLE public.vlogs_participants_video OWNER TO councilbox;

--
-- Name: vote_letters; Type: TABLE; Schema: public; Owner: councilbox
--

CREATE TABLE public.vote_letters (
    id integer NOT NULL,
    participant_id integer NOT NULL,
    date timestamp with time zone,
    signature text,
    council_id integer NOT NULL,
    signed_by integer NOT NULL
);


ALTER TABLE public.vote_letters OWNER TO councilbox;

--
-- Name: vote_letters_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.vote_letters_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.vote_letters_id_seq OWNER TO councilbox;

--
-- Name: vote_letters_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.vote_letters_id_seq OWNED BY public.vote_letters.id;


--
-- Name: votings_id_seq; Type: SEQUENCE; Schema: public; Owner: councilbox
--

CREATE SEQUENCE public.votings_id_seq
    START WITH 1
    INCREMENT BY 1
    NO MINVALUE
    NO MAXVALUE
    CACHE 1;


ALTER TABLE public.votings_id_seq OWNER TO councilbox;

--
-- Name: votings_id_seq; Type: SEQUENCE OWNED BY; Schema: public; Owner: councilbox
--

ALTER SEQUENCE public.votings_id_seq OWNED BY public.votings.id;


--
-- Name: votings_live; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.votings_live AS
 SELECT COALESCE(lpd.name, lp.name) AS name,
    COALESCE(lpd.surname, lp.surname) AS surname,
    COALESCE(lpd."position", lp."position") AS "position",
    COALESCE(lpd.email, lp.email) AS email,
    v.id,
    v.agenda_id,
    v.participant_email,
    v.code,
    v.date,
    v.cfs_code,
    v.req_code,
    v.req_text,
    v.participant_id,
    v.comment,
    v.be_on_record,
    v.vote,
    v.council_id,
    v.num_participations,
    v.delegate_id,
    v.present_vote,
    lp.name AS delegate_name,
    lp.surname AS delegate_surname,
    lp."position" AS delegate_position,
    lp.email AS delegate_email,
    lp.state,
    a.subject_type
   FROM (((public.votings v
     LEFT JOIN public.agendas a ON ((a.id = v.agenda_id)))
     LEFT JOIN public.live_participants lp ON ((lp.id = v.participant_id)))
     LEFT JOIN public.live_participants lpd ON ((lpd.id = v.delegate_id)));


ALTER TABLE public.votings_live OWNER TO councilbox;

--
-- Name: COLUMN votings_live.code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings_live.code IS 'Código que devuelve la plataforma cfs.
Por lo general 2180 es que se ha enviado correctamente.';


--
-- Name: COLUMN votings_live.cfs_code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings_live.cfs_code IS 'Código que devuelve la plataforma cfs, el cual usaremos para ver el estado de la votación de ese participante.';


--
-- Name: COLUMN votings_live.req_code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings_live.req_code IS 'Resultado de la petición a cfs con el cfs_code, que nos dice como está dicha votación.';


--
-- Name: COLUMN votings_live.be_on_record; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings_live.be_on_record IS 'Campo que nos indica si quiere que el comentario conste o no en acta.
0 --> No
1 --> Si';


--
-- Name: COLUMN votings_live.vote; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings_live.vote IS 'Guarda el resultado de la votación de una agenda de tipo votación.
0 --> Rechaza
1 --> Acepta';


--
-- Name: votings_live_fullname; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.votings_live_fullname AS
 SELECT (((lp.name)::text || ' '::text) || (lp.surname)::text) AS fullname,
    lp.dni,
    lp."position",
    v.id,
    v.agenda_id,
    v.code,
    v.date,
    v.participant_id,
    v.vote,
    v.council_id,
    v.num_participations,
    v.delegate_id,
    v.present_vote,
    (((lpd.name)::text || ' '::text) || (lpd.surname)::text) AS delegate_fullname,
    lpd.dni AS delegate_dni,
    lpd."position" AS delegate_position,
    lpd.state,
    a.subject_type
   FROM (((public.votings v
     LEFT JOIN public.agendas a ON ((a.id = v.agenda_id)))
     LEFT JOIN public.live_participants lp ON ((lp.id = v.participant_id)))
     LEFT JOIN public.live_participants lpd ON ((lpd.id = v.delegate_id)));


ALTER TABLE public.votings_live_fullname OWNER TO councilbox;

--
-- Name: COLUMN votings_live_fullname.code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings_live_fullname.code IS 'Código que devuelve la plataforma cfs.
Por lo general 2180 es que se ha enviado correctamente.';


--
-- Name: COLUMN votings_live_fullname.vote; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.votings_live_fullname.vote IS 'Guarda el resultado de la votación de una agenda de tipo votación.
0 --> Rechaza
1 --> Acepta';


--
-- Name: vquestion_votings_of_nominal; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vquestion_votings_of_nominal AS
 SELECT lp.name,
    lp.surname,
    lp."position",
    lp.email,
    lp.dni,
    v.id,
    v.council_id,
    v.question_id,
    v.option_id,
    v.participant_id,
    q.is_nominal,
    o.title
   FROM (((public.poll_question_votings v
     LEFT JOIN public.poll_questions q ON ((q.id = v.question_id)))
     LEFT JOIN public.live_participants lp ON ((lp.id = v.participant_id)))
     LEFT JOIN public.poll_question_options o ON ((o.id = v.option_id)))
  WHERE (q.is_nominal = true);


ALTER TABLE public.vquestion_votings_of_nominal OWNER TO councilbox;

--
-- Name: vsimple_book_participants; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vsimple_book_participants AS
 SELECT p.id,
    p.company_id,
    (((p.name)::text || ' '::text) || (p.surname)::text) AS name,
    p.dni,
    p.nationality,
    p.state,
    p."position",
    p.subscribe_date,
    p.unsubscribe_date,
    p.open_date,
    (((r.name)::text || ' '::text) || (r.surname)::text) AS rname,
    r.dni AS rdni,
    r.nationality AS rnationality,
    r."position" AS rposition
   FROM (public.simple_book_participants p
     LEFT JOIN public.simple_book_participants r ON ((p.delegate_id = r.id)))
  WHERE (p.type <> 2);


ALTER TABLE public.vsimple_book_participants OWNER TO councilbox;

--
-- Name: vuser_agendas; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vuser_agendas AS
 SELECT councils.id AS council_id,
    agendas.id AS agenda_id,
    user_company.user_id,
    companies.id
   FROM (((public.councils
     JOIN public.agendas ON ((councils.id = agendas.council_id)))
     JOIN public.companies ON ((councils.company_id = companies.id)))
     JOIN public.user_company ON ((companies.id = user_company.company_id)));


ALTER TABLE public.vuser_agendas OWNER TO councilbox;

--
-- Name: vuser_participants; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vuser_participants AS
 SELECT councils.id AS council_id,
    user_company.user_id,
    live_participants.id AS participant_id
   FROM (((public.councils
     JOIN public.live_participants ON ((live_participants.council_id = councils.id)))
     JOIN public.companies ON ((councils.company_id = companies.id)))
     JOIN public.user_company ON ((companies.id = user_company.company_id)));


ALTER TABLE public.vuser_participants OWNER TO councilbox;

--
-- Name: vvotings; Type: VIEW; Schema: public; Owner: councilbox
--

CREATE VIEW public.vvotings AS
 SELECT lp.name,
    lp.surname,
    lp."position",
    lp.email,
    v.id,
    v.agenda_id,
    v.participant_email,
    v.code,
    v.date,
    v.cfs_code,
    v.req_code,
    v.req_text,
    v.participant_id,
    v.comment,
    v.be_on_record,
    v.vote,
    v.council_id,
    v.num_participations,
    v.delegate_id,
    v.present_vote,
    lpd.name AS delegate_name,
    lpd.surname AS delegate_surname,
    lpd."position" AS delegate_position,
    lpd.email AS delegate_email,
    a.subject_type
   FROM (((public.votings v
     LEFT JOIN public.agendas a ON ((a.id = v.agenda_id)))
     LEFT JOIN public.live_participants lp ON ((lp.id = v.participant_id)))
     LEFT JOIN public.live_participants lpd ON ((lpd.id = v.delegate_id)));


ALTER TABLE public.vvotings OWNER TO councilbox;

--
-- Name: COLUMN vvotings.code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vvotings.code IS 'Código que devuelve la plataforma cfs.
Por lo general 2180 es que se ha enviado correctamente.';


--
-- Name: COLUMN vvotings.cfs_code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vvotings.cfs_code IS 'Código que devuelve la plataforma cfs, el cual usaremos para ver el estado de la votación de ese participante.';


--
-- Name: COLUMN vvotings.req_code; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vvotings.req_code IS 'Resultado de la petición a cfs con el cfs_code, que nos dice como está dicha votación.';


--
-- Name: COLUMN vvotings.be_on_record; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vvotings.be_on_record IS 'Campo que nos indica si quiere que el comentario conste o no en acta.
0 --> No
1 --> Si';


--
-- Name: COLUMN vvotings.vote; Type: COMMENT; Schema: public; Owner: councilbox
--

COMMENT ON COLUMN public.vvotings.vote IS 'Guarda el resultado de la votación de una agenda de tipo votación.
0 --> Rechaza
1 --> Acepta';


--
-- Name: ActAttachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."ActAttachments" ALTER COLUMN id SET DEFAULT nextval('public."ActAttachments_id_seq"'::regclass);


--
-- Name: Translations id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."Translations" ALTER COLUMN id SET DEFAULT nextval('public."Translations_id_seq"'::regclass);


--
-- Name: accounts id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.accounts ALTER COLUMN id SET DEFAULT nextval('public.accounts_id_seq'::regclass);


--
-- Name: act id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.act ALTER COLUMN id SET DEFAULT nextval('public.act_id_seq'::regclass);


--
-- Name: act_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.act_attachments ALTER COLUMN id SET DEFAULT nextval('public.act_attachments_id_seq'::regclass);


--
-- Name: agenda_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agenda_attachments ALTER COLUMN id SET DEFAULT nextval('public.agenda_attachments_id_seq'::regclass);


--
-- Name: agenda_signature_participants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agenda_signature_participants ALTER COLUMN id SET DEFAULT nextval('public.agenda_signature_participants_id_seq'::regclass);


--
-- Name: agendas id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agendas ALTER COLUMN id SET DEFAULT nextval('public.agendas_id_seq'::regclass);


--
-- Name: api_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.api_logs ALTER COLUMN id SET DEFAULT nextval('public.api_logs_id_seq'::regclass);


--
-- Name: ballots id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.ballots ALTER COLUMN id SET DEFAULT nextval('public.ballots_id_seq'::regclass);


--
-- Name: book_participant_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_logs ALTER COLUMN id SET DEFAULT nextval('public.book_participant_logs_id_seq'::regclass);


--
-- Name: book_participant_transactions id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_transactions ALTER COLUMN id SET DEFAULT nextval('public.book_participant_transactions_id_seq'::regclass);


--
-- Name: book_participants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participants ALTER COLUMN id SET DEFAULT nextval('public.book_participants_id_seq'::regclass);


--
-- Name: book_share_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_share_logs ALTER COLUMN id SET DEFAULT nextval('public.book_share_logs_id_seq'::regclass);


--
-- Name: book_shares id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_shares ALTER COLUMN id SET DEFAULT nextval('public.book_shares_id_seq'::regclass);


--
-- Name: census_participants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.census_participants ALTER COLUMN id SET DEFAULT nextval('public.census_participants_id_seq'::regclass);


--
-- Name: certificate_points id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.certificate_points ALTER COLUMN id SET DEFAULT nextval('public.certificate_points_id_seq'::regclass);


--
-- Name: certificates id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.certificates ALTER COLUMN id SET DEFAULT nextval('public.certificates_id_seq'::regclass);


--
-- Name: claudia-answers id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-answers" ALTER COLUMN id SET DEFAULT nextval('public."claudia-answers_id_seq"'::regclass);


--
-- Name: claudia-poll-questions id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-poll-questions" ALTER COLUMN id SET DEFAULT nextval('public."claudia-poll-questions_id_seq"'::regclass);


--
-- Name: claudia-poll-voters id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-poll-voters" ALTER COLUMN id SET DEFAULT nextval('public."claudia-poll-voters_id_seq"'::regclass);


--
-- Name: claudia-poll-votes id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-poll-votes" ALTER COLUMN id SET DEFAULT nextval('public."claudia-poll-votes_id_seq"'::regclass);


--
-- Name: claudia-polls id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-polls" ALTER COLUMN id SET DEFAULT nextval('public."claudia-polls_id_seq"'::regclass);


--
-- Name: claudia-raffles id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-raffles" ALTER COLUMN id SET DEFAULT nextval('public."claudia-raffles_id_seq"'::regclass);


--
-- Name: claudia-users id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-users" ALTER COLUMN id SET DEFAULT nextval('public."claudia-users_id_seq"'::regclass);


--
-- Name: comments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.comments ALTER COLUMN id SET DEFAULT nextval('public.comments_id_seq'::regclass);


--
-- Name: companies id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.companies ALTER COLUMN id SET DEFAULT nextval('public.companies_id_seq'::regclass);


--
-- Name: company_census id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_census ALTER COLUMN id SET DEFAULT nextval('public.company_census_id_seq'::regclass);


--
-- Name: company_corporates id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_corporates ALTER COLUMN id SET DEFAULT nextval('public.company_corporates_id_seq'::regclass);


--
-- Name: company_documents id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_documents ALTER COLUMN id SET DEFAULT nextval('public.company_documents_id_seq'::regclass);


--
-- Name: company_legal_texts id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_legal_texts ALTER COLUMN id SET DEFAULT nextval('public.company_legal_texts_id_seq'::regclass);


--
-- Name: company_notification_templates id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_notification_templates ALTER COLUMN id SET DEFAULT nextval('public.company_notification_templates_id_seq'::regclass);


--
-- Name: company_notifications id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_notifications ALTER COLUMN id SET DEFAULT nextval('public.company_notifications_id_seq'::regclass);


--
-- Name: company_ovac id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_ovac ALTER COLUMN id SET DEFAULT nextval('public.company_ovac_id_seq'::regclass);


--
-- Name: company_schedules id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_schedules ALTER COLUMN id SET DEFAULT nextval('public.company_schedules_id_seq'::regclass);


--
-- Name: company_statute_agenda_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agenda_attachments ALTER COLUMN id SET DEFAULT nextval('public.company_statute_agenda_attachments_id_seq'::regclass);


--
-- Name: company_statute_agendas id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agendas ALTER COLUMN id SET DEFAULT nextval('public.company_statute_agendas_id_seq'::regclass);


--
-- Name: company_statute_document_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_document_attachments ALTER COLUMN id SET DEFAULT nextval('public.company_statute_document_attachments_id_seq'::regclass);


--
-- Name: company_statute_documents id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_documents ALTER COLUMN id SET DEFAULT nextval('public.company_statute_documents_id_seq'::regclass);


--
-- Name: company_statute_user_schedules id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_user_schedules ALTER COLUMN id SET DEFAULT nextval('public.company_statute_user_schedules_id_seq'::regclass);


--
-- Name: company_statutes id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statutes ALTER COLUMN id SET DEFAULT nextval('public.company_statutes_id_seq'::regclass);


--
-- Name: company_statutes_access_restriction id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statutes_access_restriction ALTER COLUMN id SET DEFAULT nextval('public.company_statutes_access_restriction_id_seq'::regclass);


--
-- Name: company_tags id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_tags ALTER COLUMN id SET DEFAULT nextval('public.company_tags_id_seq'::regclass);


--
-- Name: corporation_drafts id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.corporation_drafts ALTER COLUMN id SET DEFAULT nextval('public.corporation_drafts_id_seq'::regclass);


--
-- Name: corporation_statutes id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.corporation_statutes ALTER COLUMN id SET DEFAULT nextval('public.corporation_statutes_id_seq'::regclass);


--
-- Name: corporations id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.corporations ALTER COLUMN id SET DEFAULT nextval('public.corporations_id_seq'::regclass);


--
-- Name: councilTemplates id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."councilTemplates" ALTER COLUMN id SET DEFAULT nextval('public."councilTemplates_id_seq"'::regclass);


--
-- Name: council_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_attachments ALTER COLUMN id SET DEFAULT nextval('public.council_attachments_id_seq'::regclass);


--
-- Name: council_conclusion_participan_signatures id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_conclusion_participan_signatures ALTER COLUMN id SET DEFAULT nextval('public.council_conclusion_participan_signatures_id_seq'::regclass);


--
-- Name: council_corporates id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_corporates ALTER COLUMN id SET DEFAULT nextval('public.council_corporates_id_seq'::regclass);


--
-- Name: council_delegates id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_delegates ALTER COLUMN id SET DEFAULT nextval('public.council_delegates_id_seq'::regclass);


--
-- Name: council_document_attachment_signatories id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_document_attachment_signatories ALTER COLUMN id SET DEFAULT nextval('public.council_document_attachment_signatories_id_seq'::regclass);


--
-- Name: council_document_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_document_attachments ALTER COLUMN id SET DEFAULT nextval('public.council_document_attachments_id_seq'::regclass);


--
-- Name: council_documents id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_documents ALTER COLUMN id SET DEFAULT nextval('public.council_documents_id_seq'::regclass);


--
-- Name: council_new_platforms id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_new_platforms ALTER COLUMN id SET DEFAULT nextval('public.council_new_platforms_id_seq'::regclass);


--
-- Name: council_participants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_participants ALTER COLUMN id SET DEFAULT nextval('public.participants_id_seq'::regclass);


--
-- Name: council_platforms id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_platforms ALTER COLUMN id SET DEFAULT nextval('public.council_platforms_id_seq'::regclass);


--
-- Name: council_portal_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_portal_attachments ALTER COLUMN id SET DEFAULT nextval('public.council_portal_attachments_id_seq'::regclass);


--
-- Name: council_portals id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_portals ALTER COLUMN id SET DEFAULT nextval('public.council_portals_id_seq'::regclass);


--
-- Name: council_statutes id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_statutes ALTER COLUMN id SET DEFAULT nextval('public.council_statutes_id_seq'::regclass);


--
-- Name: councils id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.councils ALTER COLUMN id SET DEFAULT nextval('public.councils_id_seq'::regclass);


--
-- Name: countries id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.countries ALTER COLUMN id SET DEFAULT nextval('public.countries_id_seq'::regclass);


--
-- Name: customer_survey_questions id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.customer_survey_questions ALTER COLUMN id SET DEFAULT nextval('public.customer_survey_questions_id_seq'::regclass);


--
-- Name: delegation_proxies id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.delegation_proxies ALTER COLUMN id SET DEFAULT nextval('public.delegation_proxies_id_seq'::regclass);


--
-- Name: documentAttachment id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentAttachment" ALTER COLUMN id SET DEFAULT nextval('public."documentAttachment_id_seq"'::regclass);


--
-- Name: documentParticipants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentParticipants" ALTER COLUMN id SET DEFAULT nextval('public."documentParticipants_id_seq"'::regclass);


--
-- Name: documentSignature id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentSignature" ALTER COLUMN id SET DEFAULT nextval('public."documentSignature_id_seq"'::regclass);


--
-- Name: draft_types id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.draft_types ALTER COLUMN id SET DEFAULT nextval('public.draft_types_id_seq'::regclass);


--
-- Name: drafts id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.drafts ALTER COLUMN id SET DEFAULT nextval('public.drafts_id_seq'::regclass);


--
-- Name: evidence_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.evidence_logs ALTER COLUMN id SET DEFAULT nextval('public.user_logs_id_seq'::regclass);


--
-- Name: evidences id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.evidences ALTER COLUMN id SET DEFAULT nextval('public.evidences_id_seq'::regclass);


--
-- Name: feature_exceptions id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.feature_exceptions ALTER COLUMN id SET DEFAULT nextval('public.feature_exceptions_id_seq'::regclass);


--
-- Name: features id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.features ALTER COLUMN id SET DEFAULT nextval('public.features_id_seq'::regclass);


--
-- Name: languages id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.languages ALTER COLUMN id SET DEFAULT nextval('public.languages_id_seq'::regclass);


--
-- Name: licences id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.licences ALTER COLUMN id SET DEFAULT nextval('public.licences_id_seq1'::regclass);


--
-- Name: licenses id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.licenses ALTER COLUMN id SET DEFAULT nextval('public.licences_id_seq'::regclass);


--
-- Name: live id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live ALTER COLUMN id SET DEFAULT nextval('public.live_id_seq'::regclass);


--
-- Name: live_participants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live_participants ALTER COLUMN id SET DEFAULT nextval('public.live_participants_id_seq'::regclass);


--
-- Name: live_participants_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live_participants_logs ALTER COLUMN id SET DEFAULT nextval('public.live_participants_logs_id_seq'::regclass);


--
-- Name: live_participants_signature id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live_participants_signature ALTER COLUMN id SET DEFAULT nextval('public.live_participants_signature_id_seq'::regclass);


--
-- Name: login_security_rules id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.login_security_rules ALTER COLUMN id SET DEFAULT nextval('public.login_security_rules_id_seq'::regclass);


--
-- Name: logs_participants_video id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.logs_participants_video ALTER COLUMN id SET DEFAULT nextval('public.logs_participants_video_id_seq'::regclass);


--
-- Name: meetingCelebration id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingCelebration" ALTER COLUMN id SET DEFAULT nextval('public."meetingCelebration_id_seq"'::regclass);


--
-- Name: meetingConvene id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingConvene" ALTER COLUMN id SET DEFAULT nextval('public."meetingConvene_id_seq"'::regclass);


--
-- Name: meetingParticipantConnections id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipantConnections" ALTER COLUMN id SET DEFAULT nextval('public."meetingParticipantConnections_id_seq"'::regclass);


--
-- Name: meetingParticipantSends id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipantSends" ALTER COLUMN id SET DEFAULT nextval('public."meetingParticipantSends_id_seq"'::regclass);


--
-- Name: meetingParticipants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipants" ALTER COLUMN id SET DEFAULT nextval('public."meetingParticipants_id_seq"'::regclass);


--
-- Name: meetingPlatform id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingPlatform" ALTER COLUMN id SET DEFAULT nextval('public."meetingPlatform_id_seq"'::regclass);


--
-- Name: meetings id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.meetings ALTER COLUMN id SET DEFAULT nextval('public.meetings_id_seq'::regclass);


--
-- Name: new_platforms id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.new_platforms ALTER COLUMN id SET DEFAULT nextval('public.new_platforms_id_seq'::regclass);


--
-- Name: organizations id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.organizations ALTER COLUMN id SET DEFAULT nextval('public.organizations_id_seq'::regclass);


--
-- Name: participantSms id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."participantSms" ALTER COLUMN id SET DEFAULT nextval('public."participantSms_id_seq"'::regclass);


--
-- Name: participant_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.participant_logs ALTER COLUMN id SET DEFAULT nextval('public.participant_logs_id_seq'::regclass);


--
-- Name: participant_representatives id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.participant_representatives ALTER COLUMN id SET DEFAULT nextval('public.participant_representatives_id_seq'::regclass);


--
-- Name: participant_surveys id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.participant_surveys ALTER COLUMN id SET DEFAULT nextval('public.participant_surveys_id_seq'::regclass);


--
-- Name: pdfs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.pdfs ALTER COLUMN id SET DEFAULT nextval('public.pdfs_id_seq'::regclass);


--
-- Name: platforms id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.platforms ALTER COLUMN id SET DEFAULT nextval('public.platforms_id_seq'::regclass);


--
-- Name: poll_question_options id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_options ALTER COLUMN id SET DEFAULT nextval('public.poll_question_options_id_seq'::regclass);


--
-- Name: poll_question_votings id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_votings ALTER COLUMN id SET DEFAULT nextval('public.poll_question_votings_id_seq'::regclass);


--
-- Name: poll_questions id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_questions ALTER COLUMN id SET DEFAULT nextval('public.poll_questions_id_seq'::regclass);


--
-- Name: poll_questions_options id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_questions_options ALTER COLUMN id SET DEFAULT nextval('public.poll_questions_options_id_seq'::regclass);


--
-- Name: portal_request_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.portal_request_attachments ALTER COLUMN id SET DEFAULT nextval('public.portal_request_attachments_id_seq'::regclass);


--
-- Name: provinces id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.provinces ALTER COLUMN id SET DEFAULT nextval('public.provinces_id_seq'::regclass);


--
-- Name: proxy_votes id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.proxy_votes ALTER COLUMN id SET DEFAULT nextval('public.proxy_votes_id_seq'::regclass);


--
-- Name: recording_signatures id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.recording_signatures ALTER COLUMN id SET DEFAULT nextval('public.recording_signatures_id_seq'::regclass);


--
-- Name: room_video_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.room_video_logs ALTER COLUMN id SET DEFAULT nextval('public.room_video_logs_id_seq'::regclass);


--
-- Name: scheduled_tasks id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.scheduled_tasks ALTER COLUMN id SET DEFAULT nextval('public.scheduled_tasks_id_seq'::regclass);


--
-- Name: send_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.send_attachments ALTER COLUMN id SET DEFAULT nextval('public.send_attachments_id_seq'::regclass);


--
-- Name: sends id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.sends ALTER COLUMN id SET DEFAULT nextval('public.sends_id_seq'::regclass);


--
-- Name: shareholder_request id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.shareholder_request ALTER COLUMN id SET DEFAULT nextval('public.shareholder_request_id_seq'::regclass);


--
-- Name: signature_attachments id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.signature_attachments ALTER COLUMN id SET DEFAULT nextval('public.signature_attachments_id_seq'::regclass);


--
-- Name: signature_participants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.signature_participants ALTER COLUMN id SET DEFAULT nextval('public.signature_participants_id_seq'::regclass);


--
-- Name: signature_platforms id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.signature_platforms ALTER COLUMN id SET DEFAULT nextval('public.signature_platforms_id_seq'::regclass);


--
-- Name: signatures id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.signatures ALTER COLUMN id SET DEFAULT nextval('public.signatures_id_seq'::regclass);


--
-- Name: simple_book_participant_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.simple_book_participant_logs ALTER COLUMN id SET DEFAULT nextval('public.simple_book_participant_logs_id_seq'::regclass);


--
-- Name: simple_book_participants id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.simple_book_participants ALTER COLUMN id SET DEFAULT nextval('public.simple_book_participants_id_seq'::regclass);


--
-- Name: subdomains id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.subdomains ALTER COLUMN id SET DEFAULT nextval('public.subdomains_id_seq'::regclass);


--
-- Name: translations id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.translations ALTER COLUMN id SET DEFAULT nextval('public.translations2_id_seq1'::regclass);


--
-- Name: trotter-accounts id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-accounts" ALTER COLUMN id SET DEFAULT nextval('public."trotter-accounts_id_seq"'::regclass);


--
-- Name: trotter-descriptors id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-descriptors" ALTER COLUMN id SET DEFAULT nextval('public."trotter-descriptors_id_seq"'::regclass);


--
-- Name: trotter-evidences id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-evidences" ALTER COLUMN id SET DEFAULT nextval('public."trotter-evidences_id_seq"'::regclass);


--
-- Name: trotter-feature_exceptions id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-feature_exceptions" ALTER COLUMN id SET DEFAULT nextval('public."trotter-feature_exceptions_id_seq"'::regclass);


--
-- Name: trotter-features id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-features" ALTER COLUMN id SET DEFAULT nextval('public."trotter-features_id_seq"'::regclass);


--
-- Name: trotter-organizations id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-organizations" ALTER COLUMN id SET DEFAULT nextval('public."trotter-organizations_id_seq"'::regclass);


--
-- Name: trotter-trace_descriptors id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-trace_descriptors" ALTER COLUMN id SET DEFAULT nextval('public."trotter-trace_descriptors_id_seq"'::regclass);


--
-- Name: user_company id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_company ALTER COLUMN id SET DEFAULT nextval('public.user_company_id_seq'::regclass);


--
-- Name: user_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_logs ALTER COLUMN id SET DEFAULT nextval('public.user_logs_id_seq1'::regclass);


--
-- Name: user_schedules id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_schedules ALTER COLUMN id SET DEFAULT nextval('public.user_schedules_id_seq'::regclass);


--
-- Name: user_sends id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_sends ALTER COLUMN id SET DEFAULT nextval('public.user_sends_id_seq'::regclass);


--
-- Name: user_solpheouser id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_solpheouser ALTER COLUMN id SET DEFAULT nextval('public.user_solpheouser_id_seq'::regclass);


--
-- Name: user_video_room id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_video_room ALTER COLUMN id SET DEFAULT nextval('public.user_video_room_id_seq'::regclass);


--
-- Name: users id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.users ALTER COLUMN id SET DEFAULT nextval('public.users_id_seq'::regclass);


--
-- Name: video_captures id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.video_captures ALTER COLUMN id SET DEFAULT nextval('public.video_captures_id_seq'::regclass);


--
-- Name: video_votings_logs id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.video_votings_logs ALTER COLUMN id SET DEFAULT nextval('public.video_votings_logs_id_seq'::regclass);


--
-- Name: vote_letters id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.vote_letters ALTER COLUMN id SET DEFAULT nextval('public.vote_letters_id_seq'::regclass);


--
-- Name: votings id; Type: DEFAULT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.votings ALTER COLUMN id SET DEFAULT nextval('public.votings_id_seq'::regclass);


--
-- Name: ActAttachments ActAttachments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."ActAttachments"
    ADD CONSTRAINT "ActAttachments_pkey" PRIMARY KEY (id);


--
-- Name: act PK_act; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.act
    ADD CONSTRAINT "PK_act" PRIMARY KEY (id);


--
-- Name: act_attachments PK_act_attachments; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.act_attachments
    ADD CONSTRAINT "PK_act_attachments" PRIMARY KEY (id);


--
-- Name: agendas PK_agendas; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agendas
    ADD CONSTRAINT "PK_agendas" PRIMARY KEY (id);


--
-- Name: council_attachments PK_councilAttachments; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_attachments
    ADD CONSTRAINT "PK_councilAttachments" PRIMARY KEY (id);


--
-- Name: council_participants PK_council_participants; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_participants
    ADD CONSTRAINT "PK_council_participants" PRIMARY KEY (id);


--
-- Name: councils PK_councils; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.councils
    ADD CONSTRAINT "PK_councils" PRIMARY KEY (id);


--
-- Name: languages PK_languages; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.languages
    ADD CONSTRAINT "PK_languages" PRIMARY KEY (id);


--
-- Name: live PK_live; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live
    ADD CONSTRAINT "PK_live" PRIMARY KEY (id);


--
-- Name: live_participants PK_live_participants; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live_participants
    ADD CONSTRAINT "PK_live_participants" PRIMARY KEY (id);


--
-- Name: pdfs PK_pdfs; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.pdfs
    ADD CONSTRAINT "PK_pdfs" PRIMARY KEY (id);


--
-- Name: votings PK_votings; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.votings
    ADD CONSTRAINT "PK_votings" PRIMARY KEY (id);


--
-- Name: Translations Translations_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."Translations"
    ADD CONSTRAINT "Translations_pkey" PRIMARY KEY (id);


--
-- Name: live_participants Unique_Live_participant_id; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live_participants
    ADD CONSTRAINT "Unique_Live_participant_id" UNIQUE (participant_id);


--
-- Name: accounts accounts_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.accounts
    ADD CONSTRAINT accounts_pkey PRIMARY KEY (id);


--
-- Name: act act_council_id_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.act
    ADD CONSTRAINT act_council_id_key UNIQUE (council_id);


--
-- Name: agenda_attachments agenda_attachments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agenda_attachments
    ADD CONSTRAINT agenda_attachments_pkey PRIMARY KEY (id);


--
-- Name: agenda_signature_participants agenda_signature_participants_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agenda_signature_participants
    ADD CONSTRAINT agenda_signature_participants_pkey PRIMARY KEY (id);


--
-- Name: api_logs api_logs_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.api_logs
    ADD CONSTRAINT api_logs_pkey PRIMARY KEY (id);


--
-- Name: ballots ballots_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.ballots
    ADD CONSTRAINT ballots_pkey PRIMARY KEY (id);


--
-- Name: book_participant_logs book_participant_logs_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_logs
    ADD CONSTRAINT book_participant_logs_id_pk PRIMARY KEY (id);


--
-- Name: book_participant_transactions book_participant_transactions_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_transactions
    ADD CONSTRAINT book_participant_transactions_id_pk PRIMARY KEY (id);


--
-- Name: book_participants book_participants_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participants
    ADD CONSTRAINT book_participants_id_pk PRIMARY KEY (id);


--
-- Name: book_share_logs book_share_logs_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_share_logs
    ADD CONSTRAINT book_share_logs_id_pk PRIMARY KEY (id);


--
-- Name: book_shares book_shares_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_shares
    ADD CONSTRAINT book_shares_id_pk PRIMARY KEY (id);


--
-- Name: census_participants census_participants_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.census_participants
    ADD CONSTRAINT census_participants_pkey PRIMARY KEY (id);


--
-- Name: certificate_points certificate_points_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.certificate_points
    ADD CONSTRAINT certificate_points_pkey PRIMARY KEY (id);


--
-- Name: claudia-answers claudia-answers_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-answers"
    ADD CONSTRAINT "claudia-answers_pkey" PRIMARY KEY (id);


--
-- Name: claudia-poll-questions claudia-poll-questions_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-poll-questions"
    ADD CONSTRAINT "claudia-poll-questions_pkey" PRIMARY KEY (id);


--
-- Name: claudia-poll-voters claudia-poll-voters_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-poll-voters"
    ADD CONSTRAINT "claudia-poll-voters_pkey" PRIMARY KEY (id);


--
-- Name: claudia-poll-votes claudia-poll-votes_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-poll-votes"
    ADD CONSTRAINT "claudia-poll-votes_pkey" PRIMARY KEY (id);


--
-- Name: claudia-polls claudia-polls_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-polls"
    ADD CONSTRAINT "claudia-polls_pkey" PRIMARY KEY (id);


--
-- Name: claudia-raffles claudia-raffles_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-raffles"
    ADD CONSTRAINT "claudia-raffles_pkey" PRIMARY KEY (id);


--
-- Name: claudia-users claudia-users_email_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-users"
    ADD CONSTRAINT "claudia-users_email_key" UNIQUE (email);


--
-- Name: claudia-users claudia-users_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-users"
    ADD CONSTRAINT "claudia-users_pkey" PRIMARY KEY (id);


--
-- Name: claudia-users claudia-users_username_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-users"
    ADD CONSTRAINT "claudia-users_username_key" UNIQUE (username);


--
-- Name: comments comments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.comments
    ADD CONSTRAINT comments_pkey PRIMARY KEY (id);


--
-- Name: companies companies_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.companies
    ADD CONSTRAINT companies_pkey PRIMARY KEY (id);


--
-- Name: companies companies_tin_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.companies
    ADD CONSTRAINT companies_tin_key UNIQUE (tin);


--
-- Name: company_census company_census_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_census
    ADD CONSTRAINT company_census_pkey PRIMARY KEY (id);


--
-- Name: company_corporates company_corporates_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_corporates
    ADD CONSTRAINT company_corporates_id_pk PRIMARY KEY (id);


--
-- Name: company_documents company_documents_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_documents
    ADD CONSTRAINT company_documents_pkey PRIMARY KEY (id);


--
-- Name: company_legal_texts company_legal_texts_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_legal_texts
    ADD CONSTRAINT company_legal_texts_pkey PRIMARY KEY (id);


--
-- Name: company_notification_templates company_notification_templates_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_notification_templates
    ADD CONSTRAINT company_notification_templates_pkey PRIMARY KEY (id);


--
-- Name: company_notifications company_notifications_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_notifications
    ADD CONSTRAINT company_notifications_pkey PRIMARY KEY (id);


--
-- Name: company_ovac company_ovac_company_id_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_ovac
    ADD CONSTRAINT company_ovac_company_id_key UNIQUE (company_id);


--
-- Name: company_ovac company_ovac_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_ovac
    ADD CONSTRAINT company_ovac_pkey PRIMARY KEY (id);


--
-- Name: company_schedules company_schedules_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_schedules
    ADD CONSTRAINT company_schedules_pkey PRIMARY KEY (id);


--
-- Name: company_statute_agenda_attachments company_statute_agenda_attachments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agenda_attachments
    ADD CONSTRAINT company_statute_agenda_attachments_pkey PRIMARY KEY (id);


--
-- Name: company_statute_agendas company_statute_agendas_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agendas
    ADD CONSTRAINT company_statute_agendas_pkey PRIMARY KEY (id);


--
-- Name: company_statute_document_attachments company_statute_document_attachments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_document_attachments
    ADD CONSTRAINT company_statute_document_attachments_pkey PRIMARY KEY (id);


--
-- Name: company_statute_documents company_statute_documents_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_documents
    ADD CONSTRAINT company_statute_documents_pkey PRIMARY KEY (id);


--
-- Name: company_statute_user_schedules company_statute_user_schedules_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_user_schedules
    ADD CONSTRAINT company_statute_user_schedules_pkey PRIMARY KEY (id);


--
-- Name: company_statutes_access_restriction company_statutes_access_restriction_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statutes_access_restriction
    ADD CONSTRAINT company_statutes_access_restriction_pkey PRIMARY KEY (id);


--
-- Name: company_statutes company_statutes_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statutes
    ADD CONSTRAINT company_statutes_id_pk PRIMARY KEY (id);


--
-- Name: company_tags company_tags_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_tags
    ADD CONSTRAINT company_tags_pkey PRIMARY KEY (id);


--
-- Name: company_types company_types_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_types
    ADD CONSTRAINT company_types_pkey PRIMARY KEY (value);


--
-- Name: corporation_drafts corporation_drafts_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.corporation_drafts
    ADD CONSTRAINT corporation_drafts_pkey PRIMARY KEY (id);


--
-- Name: corporation_statutes corporation_statutes_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.corporation_statutes
    ADD CONSTRAINT corporation_statutes_pkey PRIMARY KEY (id);


--
-- Name: corporations corporations_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.corporations
    ADD CONSTRAINT corporations_pkey PRIMARY KEY (id);


--
-- Name: councilTemplates councilTemplates_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."councilTemplates"
    ADD CONSTRAINT "councilTemplates_pkey" PRIMARY KEY (id);


--
-- Name: council_conclusion_participan_signatures council_conclusion_participan_signatures_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_conclusion_participan_signatures
    ADD CONSTRAINT council_conclusion_participan_signatures_pkey PRIMARY KEY (id);


--
-- Name: council_corporates council_corporates_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_corporates
    ADD CONSTRAINT council_corporates_id_pk PRIMARY KEY (id);


--
-- Name: council_delegates council_delegates_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_delegates
    ADD CONSTRAINT council_delegates_pkey PRIMARY KEY (id);


--
-- Name: council_document_attachment_signatories council_document_attachment_signatories_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_document_attachment_signatories
    ADD CONSTRAINT council_document_attachment_signatories_pk PRIMARY KEY (id);


--
-- Name: council_document_attachments council_document_attachments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_document_attachments
    ADD CONSTRAINT council_document_attachments_pkey PRIMARY KEY (id);


--
-- Name: council_documents council_documents_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_documents
    ADD CONSTRAINT council_documents_pkey PRIMARY KEY (id);


--
-- Name: council_new_platforms council_new_platforms_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_new_platforms
    ADD CONSTRAINT council_new_platforms_pkey PRIMARY KEY (id);


--
-- Name: council_platforms council_platforms_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_platforms
    ADD CONSTRAINT council_platforms_pkey PRIMARY KEY (id);


--
-- Name: council_portal_attachments council_portal_attachments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_portal_attachments
    ADD CONSTRAINT council_portal_attachments_pkey PRIMARY KEY (id);


--
-- Name: council_portals council_portals_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_portals
    ADD CONSTRAINT council_portals_pkey PRIMARY KEY (id);


--
-- Name: council_statutes council_statutes_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_statutes
    ADD CONSTRAINT council_statutes_id_pk PRIMARY KEY (id);


--
-- Name: countries countries_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.countries
    ADD CONSTRAINT countries_pkey PRIMARY KEY (id);


--
-- Name: customer_survey_questions customer_survey_questions_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.customer_survey_questions
    ADD CONSTRAINT customer_survey_questions_pkey PRIMARY KEY (id);


--
-- Name: delegation_proxies delegation_proxies_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.delegation_proxies
    ADD CONSTRAINT delegation_proxies_pkey PRIMARY KEY (id);


--
-- Name: documentAttachment documentAttachment_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentAttachment"
    ADD CONSTRAINT "documentAttachment_pkey" PRIMARY KEY (id);


--
-- Name: documentParticipants documentParticipants_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentParticipants"
    ADD CONSTRAINT "documentParticipants_pkey" PRIMARY KEY (id);


--
-- Name: documentSignature documentSignature_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentSignature"
    ADD CONSTRAINT "documentSignature_pkey" PRIMARY KEY (id);


--
-- Name: draft_types draft_types_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.draft_types
    ADD CONSTRAINT draft_types_pkey PRIMARY KEY (id);


--
-- Name: drafts drafts_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.drafts
    ADD CONSTRAINT drafts_pkey PRIMARY KEY (id);


--
-- Name: evidences evidences_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.evidences
    ADD CONSTRAINT evidences_pkey PRIMARY KEY (id);


--
-- Name: feature_exceptions feature_exceptions_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.feature_exceptions
    ADD CONSTRAINT feature_exceptions_pkey PRIMARY KEY (id);


--
-- Name: features features_name_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.features
    ADD CONSTRAINT features_name_key UNIQUE (name);


--
-- Name: features features_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.features
    ADD CONSTRAINT features_pkey PRIMARY KEY (id);


--
-- Name: licenses licences_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.licenses
    ADD CONSTRAINT licences_pkey PRIMARY KEY (id);


--
-- Name: licences licences_pkey1; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.licences
    ADD CONSTRAINT licences_pkey1 PRIMARY KEY (id);


--
-- Name: live_participants_logs live_participants_logs_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live_participants_logs
    ADD CONSTRAINT live_participants_logs_pkey PRIMARY KEY (id);


--
-- Name: live_participants_signature live_participants_signature_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live_participants_signature
    ADD CONSTRAINT live_participants_signature_pkey PRIMARY KEY (id);


--
-- Name: login_security_rules login_security_rules_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.login_security_rules
    ADD CONSTRAINT login_security_rules_pkey PRIMARY KEY (id);


--
-- Name: logs_participants_video logs_participants_video_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.logs_participants_video
    ADD CONSTRAINT logs_participants_video_pkey PRIMARY KEY (id);


--
-- Name: meetingCelebration meetingCelebration_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingCelebration"
    ADD CONSTRAINT "meetingCelebration_pkey" PRIMARY KEY (id);


--
-- Name: meetingConvene meetingConvene_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingConvene"
    ADD CONSTRAINT "meetingConvene_pkey" PRIMARY KEY (id);


--
-- Name: meetingParticipantConnections meetingParticipantConnections_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipantConnections"
    ADD CONSTRAINT "meetingParticipantConnections_pkey" PRIMARY KEY (id);


--
-- Name: meetingParticipantSends meetingParticipantSends_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipantSends"
    ADD CONSTRAINT "meetingParticipantSends_pkey" PRIMARY KEY (id);


--
-- Name: meetingParticipants meetingParticipants_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipants"
    ADD CONSTRAINT "meetingParticipants_pkey" PRIMARY KEY (id);


--
-- Name: meetingPlatform meetingPlatform_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingPlatform"
    ADD CONSTRAINT "meetingPlatform_pkey" PRIMARY KEY (id);


--
-- Name: meetings meetings_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.meetings
    ADD CONSTRAINT meetings_pkey PRIMARY KEY (id);


--
-- Name: new_platforms new_platforms_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.new_platforms
    ADD CONSTRAINT new_platforms_pkey PRIMARY KEY (id);


--
-- Name: organizations organizations_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.organizations
    ADD CONSTRAINT organizations_pkey PRIMARY KEY (id);


--
-- Name: participantSms participantSms_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."participantSms"
    ADD CONSTRAINT "participantSms_pkey" PRIMARY KEY (id);


--
-- Name: participant_representatives participant_representatives_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.participant_representatives
    ADD CONSTRAINT participant_representatives_pkey PRIMARY KEY (id);


--
-- Name: participant_surveys participant_surveys_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.participant_surveys
    ADD CONSTRAINT participant_surveys_pkey PRIMARY KEY (id);


--
-- Name: certificates pk_certificates; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.certificates
    ADD CONSTRAINT pk_certificates PRIMARY KEY (id);


--
-- Name: platforms platforms_code_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.platforms
    ADD CONSTRAINT platforms_code_key UNIQUE (code);


--
-- Name: platforms platforms_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.platforms
    ADD CONSTRAINT platforms_pkey PRIMARY KEY (id);


--
-- Name: poll_question_options poll_question_options_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_options
    ADD CONSTRAINT poll_question_options_id_pk PRIMARY KEY (id);


--
-- Name: poll_questions poll_questions_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_questions
    ADD CONSTRAINT poll_questions_id_pk PRIMARY KEY (id);


--
-- Name: poll_questions_options poll_questions_options_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_questions_options
    ADD CONSTRAINT poll_questions_options_pkey PRIMARY KEY (id);


--
-- Name: portal_request_attachments portal_request_attachments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.portal_request_attachments
    ADD CONSTRAINT portal_request_attachments_pkey PRIMARY KEY (id);


--
-- Name: provinces provinces_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.provinces
    ADD CONSTRAINT provinces_pkey PRIMARY KEY (id);


--
-- Name: proxy_votes proxy_votes_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.proxy_votes
    ADD CONSTRAINT proxy_votes_pkey PRIMARY KEY (id);


--
-- Name: recording_signatures recording_signatures_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.recording_signatures
    ADD CONSTRAINT recording_signatures_pkey PRIMARY KEY (id);


--
-- Name: scheduled_tasks scheduled_tasks_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.scheduled_tasks
    ADD CONSTRAINT scheduled_tasks_pkey PRIMARY KEY (id);


--
-- Name: schemas schemas_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.schemas
    ADD CONSTRAINT schemas_pkey PRIMARY KEY (id);


--
-- Name: send_attachments send_attachments_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.send_attachments
    ADD CONSTRAINT send_attachments_pkey PRIMARY KEY (id);


--
-- Name: shareholder_request shareholder_request_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.shareholder_request
    ADD CONSTRAINT shareholder_request_pkey PRIMARY KEY (id);


--
-- Name: simple_book_participant_logs simple_book_participant_logs_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.simple_book_participant_logs
    ADD CONSTRAINT simple_book_participant_logs_pkey PRIMARY KEY (id);


--
-- Name: simple_book_participants simple_book_participants_id_pk; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.simple_book_participants
    ADD CONSTRAINT simple_book_participants_id_pk PRIMARY KEY (id);


--
-- Name: subdomains subdomains_name_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.subdomains
    ADD CONSTRAINT subdomains_name_key UNIQUE (name);


--
-- Name: subdomains subdomains_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.subdomains
    ADD CONSTRAINT subdomains_pkey PRIMARY KEY (id);


--
-- Name: translations translations_label_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.translations
    ADD CONSTRAINT translations_label_key UNIQUE (label);


--
-- Name: translations translations_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.translations
    ADD CONSTRAINT translations_pkey PRIMARY KEY (id);


--
-- Name: trotter-accounts trotter-accounts_email_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-accounts"
    ADD CONSTRAINT "trotter-accounts_email_key" UNIQUE (email);


--
-- Name: trotter-accounts trotter-accounts_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-accounts"
    ADD CONSTRAINT "trotter-accounts_pkey" PRIMARY KEY (id);


--
-- Name: trotter-descriptors trotter-descriptors_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-descriptors"
    ADD CONSTRAINT "trotter-descriptors_pkey" PRIMARY KEY (id);


--
-- Name: trotter-evidences trotter-evidences_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-evidences"
    ADD CONSTRAINT "trotter-evidences_pkey" PRIMARY KEY (id);


--
-- Name: trotter-feature_exceptions trotter-feature_exceptions_company_id_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-feature_exceptions"
    ADD CONSTRAINT "trotter-feature_exceptions_company_id_key" UNIQUE (company_id);


--
-- Name: trotter-feature_exceptions trotter-feature_exceptions_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-feature_exceptions"
    ADD CONSTRAINT "trotter-feature_exceptions_pkey" PRIMARY KEY (id);


--
-- Name: trotter-features trotter-features_name_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-features"
    ADD CONSTRAINT "trotter-features_name_key" UNIQUE (name);


--
-- Name: trotter-features trotter-features_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-features"
    ADD CONSTRAINT "trotter-features_pkey" PRIMARY KEY (id);


--
-- Name: trotter-organizations trotter-organizations_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-organizations"
    ADD CONSTRAINT "trotter-organizations_pkey" PRIMARY KEY (id);


--
-- Name: trotter-trace_descriptors trotter-trace_descriptors_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-trace_descriptors"
    ADD CONSTRAINT "trotter-trace_descriptors_pkey" PRIMARY KEY (id);


--
-- Name: user_company user_company_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_company
    ADD CONSTRAINT user_company_pkey PRIMARY KEY (id);


--
-- Name: user_company user_company_user_id_company_id_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_company
    ADD CONSTRAINT user_company_user_id_company_id_key UNIQUE (user_id, company_id);


--
-- Name: evidence_logs user_logs_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.evidence_logs
    ADD CONSTRAINT user_logs_pkey PRIMARY KEY (id);


--
-- Name: user_logs user_logs_pkey1; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_logs
    ADD CONSTRAINT user_logs_pkey1 PRIMARY KEY (id);


--
-- Name: user_schedules user_schedules_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_schedules
    ADD CONSTRAINT user_schedules_pkey PRIMARY KEY (id);


--
-- Name: user_solpheouser user_solpheouser_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_solpheouser
    ADD CONSTRAINT user_solpheouser_pkey PRIMARY KEY (id);


--
-- Name: user_video_room user_video_room_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_video_room
    ADD CONSTRAINT user_video_room_pkey PRIMARY KEY (id);


--
-- Name: users users_email_key; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.users
    ADD CONSTRAINT users_email_key UNIQUE (email);


--
-- Name: users users_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.users
    ADD CONSTRAINT users_pkey PRIMARY KEY (id);


--
-- Name: video_captures video_captures_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.video_captures
    ADD CONSTRAINT video_captures_pkey PRIMARY KEY (id);


--
-- Name: video_votings_logs video_votings_logs_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.video_votings_logs
    ADD CONSTRAINT video_votings_logs_pkey PRIMARY KEY (id);


--
-- Name: vote_letters vote_letters_pkey; Type: CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.vote_letters
    ADD CONSTRAINT vote_letters_pkey PRIMARY KEY (id);


--
-- Name: agenda_attachments__agenda_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX agenda_attachments__agenda_id_index ON public.agenda_attachments USING btree (agenda_id);


--
-- Name: agenda_attachments__council_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX agenda_attachments__council_id_index ON public.agenda_attachments USING btree (council_id);


--
-- Name: agendas__index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX agendas__index ON public.agendas USING btree (council_id, order_index);


--
-- Name: book_participant_logs_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX book_participant_logs_id_uindex ON public.book_participant_logs USING btree (id);


--
-- Name: book_participant_transactions_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX book_participant_transactions_id_uindex ON public.book_participant_transactions USING btree (id);


--
-- Name: book_participants_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX book_participants_id_uindex ON public.book_participants USING btree (id);


--
-- Name: book_share_logs_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX book_share_logs_id_uindex ON public.book_share_logs USING btree (id);


--
-- Name: book_shares_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX book_shares_id_uindex ON public.book_shares USING btree (id);


--
-- Name: census_participants_census_id_email_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX census_participants_census_id_email_uindex ON public.census_participants USING btree (census_id, lower((email)::text));


--
-- Name: census_participants_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX census_participants_id_uindex ON public.census_participants USING btree (id);


--
-- Name: claudia-answers_question_id_vote_id; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX "claudia-answers_question_id_vote_id" ON public."claudia-answers" USING btree (question_id, vote_id);


--
-- Name: claudia-poll-voters_vote_id_poll_id; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX "claudia-poll-voters_vote_id_poll_id" ON public."claudia-poll-voters" USING btree (vote_id, poll_id);


--
-- Name: company_census_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX company_census_id_uindex ON public.company_census USING btree (id);


--
-- Name: company_statutes_company_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX company_statutes_company_id_index ON public.company_statutes USING btree (company_id);


--
-- Name: company_types_value_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX company_types_value_uindex ON public.company_types USING btree (value);


--
-- Name: corporations_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX corporations_id_uindex ON public.corporations USING btree (id);


--
-- Name: council_delegates_council_id_participant_id; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX council_delegates_council_id_participant_id ON public.council_delegates USING btree (council_id, participant_id);


--
-- Name: council_document_attachment_signatories_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX council_document_attachment_signatories_id_uindex ON public.council_document_attachment_signatories USING btree (id);


--
-- Name: council_participants_council_id_email_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX council_participants_council_id_email_uindex ON public.council_participants USING btree (council_id, lower((email)::text));


--
-- Name: council_statutes_council_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX council_statutes_council_id_index ON public.council_statutes USING btree (council_id);


--
-- Name: delegation_proxies_delegate_id_participant_id; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX delegation_proxies_delegate_id_participant_id ON public.delegation_proxies USING btree (delegate_id, participant_id);


--
-- Name: evidence_logs__index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX evidence_logs__index ON public.evidence_logs USING btree (participant_id, type);


--
-- Name: live_participants_council_id_email_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX live_participants_council_id_email_uindex ON public.live_participants USING btree (council_id, lower((email)::text));


--
-- Name: live_participants_council_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX live_participants_council_id_index ON public.live_participants USING btree (council_id);


--
-- Name: live_participants_state_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX live_participants_state_index ON public.live_participants USING btree (state);


--
-- Name: new_platforms_company_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX new_platforms_company_id_uindex ON public.new_platforms USING btree (company_id);


--
-- Name: participant_representatives_participant_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX participant_representatives_participant_id_index ON public.participant_representatives USING btree (participant_id);


--
-- Name: participant_representatives_representative_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX participant_representatives_representative_id_index ON public.participant_representatives USING btree (representative_id);


--
-- Name: participant_representatives_representative_id_participant_id; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX participant_representatives_representative_id_participant_id ON public.participant_representatives USING btree (representative_id, participant_id);


--
-- Name: sends_code_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX sends_code_index ON public.sends USING btree (code);


--
-- Name: sends_participant_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX sends_participant_id_index ON public.sends USING btree (participant_id);


--
-- Name: simple_book_participant_logs_id_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX simple_book_participant_logs_id_uindex ON public.simple_book_participant_logs USING btree (id);


--
-- Name: simple_book_participants_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX simple_book_participants_id_index ON public.simple_book_participants USING btree (id);


--
-- Name: user_company_user_id_company_id; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX user_company_user_id_company_id ON public.user_company USING btree (user_id, company_id);


--
-- Name: users_code_uindex; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE UNIQUE INDEX users_code_uindex ON public.users USING btree (code);


--
-- Name: votings__index_agenda_id; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX votings__index_agenda_id ON public.votings USING btree (agenda_id);


--
-- Name: votings__index_participant_id; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX votings__index_participant_id ON public.votings USING btree (participant_id);


--
-- Name: votings_delegate_id_index; Type: INDEX; Schema: public; Owner: councilbox
--

CREATE INDEX votings_delegate_id_index ON public.votings USING btree (delegate_id);


--
-- Name: ActAttachments ActAttachments_council_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."ActAttachments"
    ADD CONSTRAINT "ActAttachments_council_id_fkey" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: act_attachments FK_act_attachments_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.act_attachments
    ADD CONSTRAINT "FK_act_attachments_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: act FK_act_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.act
    ADD CONSTRAINT "FK_act_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: agenda_attachments FK_agenda_attachments_agendas; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agenda_attachments
    ADD CONSTRAINT "FK_agenda_attachments_agendas" FOREIGN KEY (agenda_id) REFERENCES public.agendas(id);


--
-- Name: agendas FK_agendas_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agendas
    ADD CONSTRAINT "FK_agendas_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: council_attachments FK_council_attachments_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_attachments
    ADD CONSTRAINT "FK_council_attachments_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: council_participants FK_council_participants_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_participants
    ADD CONSTRAINT "FK_council_participants_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: drafts FK_drafts_companies; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.drafts
    ADD CONSTRAINT "FK_drafts_companies" FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: live FK_live_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live
    ADD CONSTRAINT "FK_live_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: live_participants FK_live_participants_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.live_participants
    ADD CONSTRAINT "FK_live_participants_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: pdfs FK_pdfs_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.pdfs
    ADD CONSTRAINT "FK_pdfs_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: votings FK_votings_agendas; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.votings
    ADD CONSTRAINT "FK_votings_agendas" FOREIGN KEY (agenda_id) REFERENCES public.agendas(id);


--
-- Name: votings FK_votings_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.votings
    ADD CONSTRAINT "FK_votings_councils" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: agenda_signature_participants agenda_signature_participants_agenda_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agenda_signature_participants
    ADD CONSTRAINT agenda_signature_participants_agenda_id_fkey FOREIGN KEY (agenda_id) REFERENCES public.agendas(id);


--
-- Name: agenda_signature_participants agenda_signature_participants_participant_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.agenda_signature_participants
    ADD CONSTRAINT agenda_signature_participants_participant_id_fkey FOREIGN KEY (participant_id) REFERENCES public.live_participants(id);


--
-- Name: ballots ballots_agenda_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.ballots
    ADD CONSTRAINT ballots_agenda_id_fkey FOREIGN KEY (agenda_id) REFERENCES public.agendas(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: book_participant_logs book_participant_logs_book_participants_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_logs
    ADD CONSTRAINT book_participant_logs_book_participants_id_fk FOREIGN KEY (participant_id) REFERENCES public.book_participants(id);


--
-- Name: book_participant_logs book_participant_logs_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_logs
    ADD CONSTRAINT book_participant_logs_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: book_participant_transactions book_participant_transactions_book_participants_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_transactions
    ADD CONSTRAINT book_participant_transactions_book_participants_id_fk FOREIGN KEY (participant_id) REFERENCES public.book_participants(id);


--
-- Name: book_participant_transactions book_participant_transactions_book_shares_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_transactions
    ADD CONSTRAINT book_participant_transactions_book_shares_id_fk FOREIGN KEY (share_id) REFERENCES public.book_shares(id);


--
-- Name: book_participant_transactions book_participant_transactions_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participant_transactions
    ADD CONSTRAINT book_participant_transactions_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: book_participants book_participants_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_participants
    ADD CONSTRAINT book_participants_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: book_share_logs book_share_logs_book_shares_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_share_logs
    ADD CONSTRAINT book_share_logs_book_shares_id_fk FOREIGN KEY (share_id) REFERENCES public.book_shares(id);


--
-- Name: book_shares book_shares_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.book_shares
    ADD CONSTRAINT book_shares_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: census_participants census_participants_census_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.census_participants
    ADD CONSTRAINT census_participants_census_id_fkey FOREIGN KEY (census_id) REFERENCES public.company_census(id);


--
-- Name: census_participants census_participants_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.census_participants
    ADD CONSTRAINT census_participants_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: claudia-poll-questions claudia-poll-questions_poll_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-poll-questions"
    ADD CONSTRAINT "claudia-poll-questions_poll_id_fkey" FOREIGN KEY (poll_id) REFERENCES public."claudia-polls"(id) ON UPDATE CASCADE ON DELETE CASCADE;


--
-- Name: claudia-polls claudia-polls_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."claudia-polls"
    ADD CONSTRAINT "claudia-polls_id_fkey" FOREIGN KEY (id) REFERENCES public."claudia-polls"(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: company_census company_census_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_census
    ADD CONSTRAINT company_census_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: company_corporates company_corporates_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_corporates
    ADD CONSTRAINT company_corporates_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: company_documents company_documents_company_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_documents
    ADD CONSTRAINT company_documents_company_id_fkey FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: company_legal_texts company_legal_texts_company_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_legal_texts
    ADD CONSTRAINT company_legal_texts_company_id_fkey FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: company_notification_templates company_notification_templates_company_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_notification_templates
    ADD CONSTRAINT company_notification_templates_company_id_fkey FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: company_notifications company_notifications_company_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_notifications
    ADD CONSTRAINT company_notifications_company_id_fkey FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: company_ovac company_ovac_company_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_ovac
    ADD CONSTRAINT company_ovac_company_id_fkey FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: company_statute_agenda_attachments company_statute_agenda_attachments_agenda_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agenda_attachments
    ADD CONSTRAINT company_statute_agenda_attachments_agenda_id_fkey FOREIGN KEY (agenda_id) REFERENCES public.company_statute_agendas(id);


--
-- Name: company_statute_agendas company_statute_agendas_company_statute_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agendas
    ADD CONSTRAINT company_statute_agendas_company_statute_id_fkey FOREIGN KEY (company_statute_id) REFERENCES public.company_statutes(id);


--
-- Name: company_statute_agendas company_statute_agendas_creator_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agendas
    ADD CONSTRAINT company_statute_agendas_creator_id_fkey FOREIGN KEY (creator_id) REFERENCES public.users(id);


--
-- Name: company_statute_agendas company_statute_agendas_draft_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agendas
    ADD CONSTRAINT company_statute_agendas_draft_id_fkey FOREIGN KEY (draft_id) REFERENCES public.drafts(id);


--
-- Name: company_statute_agendas company_statute_agendas_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_agendas
    ADD CONSTRAINT company_statute_agendas_id_fkey FOREIGN KEY (id) REFERENCES public.company_census(id) ON UPDATE CASCADE ON DELETE CASCADE;


--
-- Name: company_statute_document_attachments company_statute_document_attac_company_statute_document_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_document_attachments
    ADD CONSTRAINT company_statute_document_attac_company_statute_document_id_fkey FOREIGN KEY (company_statute_document_id) REFERENCES public.company_statute_documents(id);


--
-- Name: company_statute_documents company_statute_documents_company_statute_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_documents
    ADD CONSTRAINT company_statute_documents_company_statute_id_fkey FOREIGN KEY (company_statute_id) REFERENCES public.company_statutes(id);


--
-- Name: company_statute_user_schedules company_statute_user_schedules_company_statute_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statute_user_schedules
    ADD CONSTRAINT company_statute_user_schedules_company_statute_id_fkey FOREIGN KEY (company_statute_id) REFERENCES public.company_statutes(id);


--
-- Name: company_statutes company_statutes_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_statutes
    ADD CONSTRAINT company_statutes_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: company_tags company_tags_company_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.company_tags
    ADD CONSTRAINT company_tags_company_id_fkey FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: corporation_statutes corporation_statutes_corporation_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.corporation_statutes
    ADD CONSTRAINT corporation_statutes_corporation_id_fkey FOREIGN KEY (corporation_id) REFERENCES public.corporations(id);


--
-- Name: councilTemplates councilTemplates_councilId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."councilTemplates"
    ADD CONSTRAINT "councilTemplates_councilId_fkey" FOREIGN KEY ("councilId") REFERENCES public.councils(id);


--
-- Name: council_conclusion_participan_signatures council_conclusion_participan_signatures_participant_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_conclusion_participan_signatures
    ADD CONSTRAINT council_conclusion_participan_signatures_participant_id_fkey FOREIGN KEY (participant_id) REFERENCES public.live_participants(id) ON UPDATE CASCADE;


--
-- Name: council_corporates council_corporates_councils_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_corporates
    ADD CONSTRAINT council_corporates_councils_id_fk FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: council_delegates council_delegates_council_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_delegates
    ADD CONSTRAINT council_delegates_council_id_fkey FOREIGN KEY (council_id) REFERENCES public.councils(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: council_delegates council_delegates_participant_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_delegates
    ADD CONSTRAINT council_delegates_participant_id_fkey FOREIGN KEY (participant_id) REFERENCES public.council_participants(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: council_document_attachments council_document_attachments_council_document_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_document_attachments
    ADD CONSTRAINT council_document_attachments_council_document_id_fkey FOREIGN KEY (council_document_id) REFERENCES public.council_documents(id);


--
-- Name: council_platforms council_platforms_councils_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_platforms
    ADD CONSTRAINT council_platforms_councils_id_fk FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: council_portal_attachments council_portal_attachments_portal_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_portal_attachments
    ADD CONSTRAINT council_portal_attachments_portal_id_fkey FOREIGN KEY (portal_id) REFERENCES public.council_portals(id);


--
-- Name: council_portals council_portals_council_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_portals
    ADD CONSTRAINT council_portals_council_id_fkey FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: council_statutes council_statutes_councils_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.council_statutes
    ADD CONSTRAINT council_statutes_councils_id_fk FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: documentAttachment documentAttachment_signatureId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentAttachment"
    ADD CONSTRAINT "documentAttachment_signatureId_fkey" FOREIGN KEY ("signatureId") REFERENCES public."documentSignature"(id) ON UPDATE CASCADE ON DELETE CASCADE;


--
-- Name: documentParticipants documentParticipants_signatureId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentParticipants"
    ADD CONSTRAINT "documentParticipants_signatureId_fkey" FOREIGN KEY ("signatureId") REFERENCES public."documentSignature"(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: documentSignature documentSignature_companyId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."documentSignature"
    ADD CONSTRAINT "documentSignature_companyId_fkey" FOREIGN KEY ("companyId") REFERENCES public.companies(id) ON UPDATE CASCADE;


--
-- Name: certificates fk_certificates_councils; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.certificates
    ADD CONSTRAINT fk_certificates_councils FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: user_solpheouser fk_user_id; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_solpheouser
    ADD CONSTRAINT fk_user_id FOREIGN KEY (user_id) REFERENCES public.users(id);


--
-- Name: login_security_rules login_security_rules_company_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.login_security_rules
    ADD CONSTRAINT login_security_rules_company_id_fkey FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: meetingCelebration meetingCelebration_meetingId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingCelebration"
    ADD CONSTRAINT "meetingCelebration_meetingId_fkey" FOREIGN KEY ("meetingId") REFERENCES public.meetings(id);


--
-- Name: meetingConvene meetingConvene_meetingId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingConvene"
    ADD CONSTRAINT "meetingConvene_meetingId_fkey" FOREIGN KEY ("meetingId") REFERENCES public.meetings(id);


--
-- Name: meetingConvene meetingConvene_userId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingConvene"
    ADD CONSTRAINT "meetingConvene_userId_fkey" FOREIGN KEY ("userId") REFERENCES public.users(id);


--
-- Name: meetingParticipantConnections meetingParticipantConnections_meetingId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipantConnections"
    ADD CONSTRAINT "meetingParticipantConnections_meetingId_fkey" FOREIGN KEY ("meetingId") REFERENCES public.meetings(id);


--
-- Name: meetingParticipantConnections meetingParticipantConnections_participantId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipantConnections"
    ADD CONSTRAINT "meetingParticipantConnections_participantId_fkey" FOREIGN KEY ("participantId") REFERENCES public."meetingParticipants"(id);


--
-- Name: meetingParticipantSends meetingParticipantSends_meetingId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipantSends"
    ADD CONSTRAINT "meetingParticipantSends_meetingId_fkey" FOREIGN KEY ("meetingId") REFERENCES public.meetings(id);


--
-- Name: meetingParticipantSends meetingParticipantSends_participantId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipantSends"
    ADD CONSTRAINT "meetingParticipantSends_participantId_fkey" FOREIGN KEY ("participantId") REFERENCES public."meetingParticipants"(id) ON UPDATE CASCADE;


--
-- Name: meetingParticipants meetingParticipants_meetingId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingParticipants"
    ADD CONSTRAINT "meetingParticipants_meetingId_fkey" FOREIGN KEY ("meetingId") REFERENCES public.meetings(id);


--
-- Name: meetingPlatform meetingPlatform_meetingId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."meetingPlatform"
    ADD CONSTRAINT "meetingPlatform_meetingId_fkey" FOREIGN KEY ("meetingId") REFERENCES public.meetings(id);


--
-- Name: meetings meetings_companyId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.meetings
    ADD CONSTRAINT "meetings_companyId_fkey" FOREIGN KEY ("companyId") REFERENCES public.companies(id);


--
-- Name: new_platforms new_platforms_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.new_platforms
    ADD CONSTRAINT new_platforms_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: organizations organizations_parent_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.organizations
    ADD CONSTRAINT organizations_parent_fkey FOREIGN KEY (parent) REFERENCES public.organizations(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: participantSms participantSms_councilId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."participantSms"
    ADD CONSTRAINT "participantSms_councilId_fkey" FOREIGN KEY ("councilId") REFERENCES public.councils(id);


--
-- Name: participantSms participantSms_liveParticipantId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."participantSms"
    ADD CONSTRAINT "participantSms_liveParticipantId_fkey" FOREIGN KEY ("liveParticipantId") REFERENCES public.live_participants(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: participantSms participantSms_participantId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."participantSms"
    ADD CONSTRAINT "participantSms_participantId_fkey" FOREIGN KEY (participant_id) REFERENCES public.council_participants(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: participant_surveys participant_surveys_councilId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.participant_surveys
    ADD CONSTRAINT "participant_surveys_councilId_fkey" FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: participant_surveys participant_surveys_participantId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.participant_surveys
    ADD CONSTRAINT "participant_surveys_participantId_fkey" FOREIGN KEY (participant_id) REFERENCES public.live_participants(id);


--
-- Name: poll_question_options poll_question_options_poll_questions_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_options
    ADD CONSTRAINT poll_question_options_poll_questions_id_fk FOREIGN KEY (question_id) REFERENCES public.poll_questions(id);


--
-- Name: poll_question_votings poll_question_votings_live_participants_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_votings
    ADD CONSTRAINT poll_question_votings_live_participants_id_fk FOREIGN KEY (participant_id) REFERENCES public.live_participants(id);


--
-- Name: poll_question_votings poll_question_votings_poll_question_options_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_votings
    ADD CONSTRAINT poll_question_votings_poll_question_options_id_fk FOREIGN KEY (option_id) REFERENCES public.poll_question_options(id);


--
-- Name: poll_questions poll_questions_councils_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_questions
    ADD CONSTRAINT poll_questions_councils_id_fk FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: poll_questions_options poll_questions_options_agenda_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_questions_options
    ADD CONSTRAINT poll_questions_options_agenda_id_fkey FOREIGN KEY (agenda_id) REFERENCES public.agendas(id) ON UPDATE CASCADE ON DELETE CASCADE;


--
-- Name: poll_question_options poll_questions_options_councils_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_options
    ADD CONSTRAINT poll_questions_options_councils_id_fk FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: poll_question_votings poll_questions_votings_councils_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_votings
    ADD CONSTRAINT poll_questions_votings_councils_id_fk FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: portal_request_attachments portal_request_attachments_request_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.portal_request_attachments
    ADD CONSTRAINT portal_request_attachments_request_id_fkey FOREIGN KEY (request_id) REFERENCES public.shareholder_request(id);


--
-- Name: poll_question_votings ppoll_question_votings_poll_questions_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.poll_question_votings
    ADD CONSTRAINT ppoll_question_votings_poll_questions_id_fk FOREIGN KEY (question_id) REFERENCES public.poll_questions(id);


--
-- Name: sends sends_councils_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.sends
    ADD CONSTRAINT sends_councils_id_fk FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: simple_book_participants simple_book_participants_companies_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.simple_book_participants
    ADD CONSTRAINT simple_book_participants_companies_id_fk FOREIGN KEY (company_id) REFERENCES public.companies(id);


--
-- Name: trotter-feature_exceptions trotter-feature_exceptions_featureId_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public."trotter-feature_exceptions"
    ADD CONSTRAINT "trotter-feature_exceptions_featureId_fkey" FOREIGN KEY ("featureId") REFERENCES public."trotter-features"(id) ON UPDATE CASCADE ON DELETE SET NULL;


--
-- Name: user_logs user_logs_user_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_logs
    ADD CONSTRAINT user_logs_user_id_fkey FOREIGN KEY (user_id) REFERENCES public.users(id);


--
-- Name: user_video_room user_video_room_user_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.user_video_room
    ADD CONSTRAINT user_video_room_user_id_fkey FOREIGN KEY (user_id) REFERENCES public.users(id);


--
-- Name: video_votings_logs video_votings_logs_agenda_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.video_votings_logs
    ADD CONSTRAINT video_votings_logs_agenda_id_fkey FOREIGN KEY (agenda_id) REFERENCES public.agendas(id);


--
-- Name: video_votings_logs video_votings_logs_council_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.video_votings_logs
    ADD CONSTRAINT video_votings_logs_council_id_fkey FOREIGN KEY (council_id) REFERENCES public.councils(id);


--
-- Name: video_votings_logs video_votings_logs_participant_id_fkey; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.video_votings_logs
    ADD CONSTRAINT video_votings_logs_participant_id_fkey FOREIGN KEY (participant_id) REFERENCES public.live_participants(id);


--
-- Name: video_votings_logs video_votings_logs_votings_id_fk; Type: FK CONSTRAINT; Schema: public; Owner: councilbox
--

ALTER TABLE ONLY public.video_votings_logs
    ADD CONSTRAINT video_votings_logs_votings_id_fk FOREIGN KEY (voting_id) REFERENCES public.votings(id);


--
-- Name: SCHEMA public; Type: ACL; Schema: -; Owner: azure_superuser
--

GRANT USAGE ON SCHEMA public TO councilbox_readonly;


--
-- Name: TABLE "ActAttachments"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."ActAttachments" TO councilbox_readonly;


--
-- Name: TABLE "Translations"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."Translations" TO councilbox_readonly;


--
-- Name: TABLE accounts; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.accounts TO councilbox_readonly;


--
-- Name: TABLE act; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.act TO "councilbox-horus";
GRANT SELECT ON TABLE public.act TO councilbox_readonly;


--
-- Name: TABLE act_attachments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.act_attachments TO "councilbox-horus";
GRANT SELECT ON TABLE public.act_attachments TO councilbox_readonly;


--
-- Name: SEQUENCE act_attachments_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.act_attachments_id_seq TO "councilbox-horus";


--
-- Name: SEQUENCE act_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.act_id_seq TO "councilbox-horus";


--
-- Name: TABLE council_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.council_participants TO "councilbox-horus";
GRANT SELECT ON TABLE public.council_participants TO councilbox_readonly;


--
-- Name: TABLE sends; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.sends TO "councilbox-horus";
GRANT SELECT ON TABLE public.sends TO councilbox_readonly;


--
-- Name: TABLE act_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.act_participants TO councilbox_readonly;


--
-- Name: TABLE agenda_attachments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.agenda_attachments TO "councilbox-horus";
GRANT SELECT ON TABLE public.agenda_attachments TO councilbox_readonly;


--
-- Name: SEQUENCE agenda_attachments_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.agenda_attachments_id_seq TO "councilbox-horus";


--
-- Name: TABLE agendas; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.agendas TO "councilbox-horus";
GRANT SELECT ON TABLE public.agendas TO councilbox_readonly;


--
-- Name: SEQUENCE agendas_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.agendas_id_seq TO "councilbox-horus";


--
-- Name: TABLE api_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.api_logs TO councilbox_readonly;


--
-- Name: TABLE councils; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.councils TO "councilbox-horus";
GRANT SELECT ON TABLE public.councils TO councilbox_readonly;


--
-- Name: TABLE live_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.live_participants TO "councilbox-horus";
GRANT SELECT ON TABLE public.live_participants TO councilbox_readonly;


--
-- Name: TABLE assistants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.assistants TO councilbox_readonly;


--
-- Name: TABLE live_participants_signature; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.live_participants_signature TO "councilbox-horus";
GRANT SELECT ON TABLE public.live_participants_signature TO councilbox_readonly;


--
-- Name: TABLE ballots; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.ballots TO councilbox_readonly;


--
-- Name: TABLE book_participant_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.book_participant_logs TO "councilbox-horus";
GRANT SELECT ON TABLE public.book_participant_logs TO councilbox_readonly;


--
-- Name: SEQUENCE book_participant_logs_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.book_participant_logs_id_seq TO "councilbox-horus";


--
-- Name: TABLE book_participant_transactions; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.book_participant_transactions TO "councilbox-horus";
GRANT SELECT ON TABLE public.book_participant_transactions TO councilbox_readonly;


--
-- Name: SEQUENCE book_participant_transactions_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.book_participant_transactions_id_seq TO "councilbox-horus";


--
-- Name: TABLE book_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.book_participants TO "councilbox-horus";
GRANT SELECT ON TABLE public.book_participants TO councilbox_readonly;


--
-- Name: SEQUENCE book_participants_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.book_participants_id_seq TO "councilbox-horus";


--
-- Name: TABLE book_shares; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.book_shares TO "councilbox-horus";
GRANT SELECT ON TABLE public.book_shares TO councilbox_readonly;


--
-- Name: TABLE book_participants_recount_by_share; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.book_participants_recount_by_share TO "councilbox-horus";
GRANT SELECT ON TABLE public.book_participants_recount_by_share TO councilbox_readonly;


--
-- Name: TABLE book_participants_recount; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.book_participants_recount TO "councilbox-horus";
GRANT SELECT ON TABLE public.book_participants_recount TO councilbox_readonly;


--
-- Name: TABLE book_share_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.book_share_logs TO "councilbox-horus";
GRANT SELECT ON TABLE public.book_share_logs TO councilbox_readonly;


--
-- Name: SEQUENCE book_share_logs_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.book_share_logs_id_seq TO "councilbox-horus";


--
-- Name: SEQUENCE book_shares_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.book_shares_id_seq TO "councilbox-horus";


--
-- Name: TABLE census_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.census_participants TO "councilbox-horus";
GRANT SELECT ON TABLE public.census_participants TO councilbox_readonly;


--
-- Name: SEQUENCE census_participants_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.census_participants_id_seq TO "councilbox-horus";


--
-- Name: TABLE certificate_points; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.certificate_points TO "councilbox-horus";
GRANT SELECT ON TABLE public.certificate_points TO councilbox_readonly;


--
-- Name: SEQUENCE certificate_points_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.certificate_points_id_seq TO "councilbox-horus";


--
-- Name: TABLE certificates; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.certificates TO "councilbox-horus";
GRANT SELECT ON TABLE public.certificates TO councilbox_readonly;


--
-- Name: SEQUENCE certificates_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.certificates_id_seq TO "councilbox-horus";


--
-- Name: TABLE "claudia-answers"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."claudia-answers" TO councilbox_readonly;


--
-- Name: TABLE "claudia-poll-questions"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."claudia-poll-questions" TO councilbox_readonly;


--
-- Name: TABLE "claudia-poll-voters"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."claudia-poll-voters" TO councilbox_readonly;


--
-- Name: TABLE "claudia-poll-votes"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."claudia-poll-votes" TO councilbox_readonly;


--
-- Name: TABLE "claudia-polls"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."claudia-polls" TO councilbox_readonly;


--
-- Name: TABLE "claudia-raffles"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."claudia-raffles" TO councilbox_readonly;


--
-- Name: TABLE "claudia-users"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."claudia-users" TO councilbox_readonly;


--
-- Name: TABLE comments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.comments TO "councilbox-horus";
GRANT SELECT ON TABLE public.comments TO councilbox_readonly;


--
-- Name: SEQUENCE comments_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.comments_id_seq TO "councilbox-horus";


--
-- Name: TABLE companies; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.companies TO "councilbox-horus";
GRANT SELECT ON TABLE public.companies TO councilbox_readonly;


--
-- Name: SEQUENCE companies_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.companies_id_seq TO "councilbox-horus";


--
-- Name: TABLE company_census; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.company_census TO "councilbox-horus";
GRANT SELECT ON TABLE public.company_census TO councilbox_readonly;


--
-- Name: SEQUENCE company_census_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.company_census_id_seq TO "councilbox-horus";


--
-- Name: TABLE company_corporates; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.company_corporates TO "councilbox-horus";
GRANT SELECT ON TABLE public.company_corporates TO councilbox_readonly;


--
-- Name: SEQUENCE company_corporates_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.company_corporates_id_seq TO "councilbox-horus";


--
-- Name: TABLE company_documents; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.company_documents TO councilbox_readonly;


--
-- Name: TABLE company_notifications; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.company_notifications TO councilbox_readonly;


--
-- Name: TABLE company_ovac; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.company_ovac TO councilbox_readonly;


--
-- Name: TABLE company_statutes; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.company_statutes TO "councilbox-horus";
GRANT SELECT ON TABLE public.company_statutes TO councilbox_readonly;


--
-- Name: TABLE company_statutes_access_restriction; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.company_statutes_access_restriction TO councilbox_readonly;


--
-- Name: SEQUENCE company_statutes_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.company_statutes_id_seq TO "councilbox-horus";


--
-- Name: TABLE company_tags; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.company_tags TO councilbox_readonly;


--
-- Name: TABLE company_types; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.company_types TO "councilbox-horus";
GRANT SELECT ON TABLE public.company_types TO councilbox_readonly;


--
-- Name: TABLE corporation_drafts; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.corporation_drafts TO "councilbox-horus";
GRANT SELECT ON TABLE public.corporation_drafts TO councilbox_readonly;


--
-- Name: SEQUENCE corporation_drafts_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.corporation_drafts_id_seq TO "councilbox-horus";


--
-- Name: TABLE corporation_statutes; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.corporation_statutes TO "councilbox-horus";
GRANT SELECT ON TABLE public.corporation_statutes TO councilbox_readonly;


--
-- Name: SEQUENCE corporation_statutes_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.corporation_statutes_id_seq TO "councilbox-horus";


--
-- Name: TABLE corporations; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.corporations TO "councilbox-horus";
GRANT SELECT ON TABLE public.corporations TO councilbox_readonly;


--
-- Name: SEQUENCE corporations_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.corporations_id_seq TO "councilbox-horus";


--
-- Name: TABLE "councilTemplates"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."councilTemplates" TO "councilbox-horus";
GRANT SELECT ON TABLE public."councilTemplates" TO councilbox_readonly;


--
-- Name: SEQUENCE "councilTemplates_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."councilTemplates_id_seq" TO "councilbox-horus";


--
-- Name: TABLE council_attachments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.council_attachments TO "councilbox-horus";
GRANT SELECT ON TABLE public.council_attachments TO councilbox_readonly;


--
-- Name: SEQUENCE council_attachments_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.council_attachments_id_seq TO "councilbox-horus";


--
-- Name: TABLE council_corporates; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.council_corporates TO "councilbox-horus";
GRANT SELECT ON TABLE public.council_corporates TO councilbox_readonly;


--
-- Name: SEQUENCE council_corporates_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.council_corporates_id_seq TO "councilbox-horus";


--
-- Name: TABLE council_delegates; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.council_delegates TO councilbox_readonly;


--
-- Name: TABLE council_new_platforms; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.council_new_platforms TO "councilbox-horus";
GRANT SELECT ON TABLE public.council_new_platforms TO councilbox_readonly;


--
-- Name: SEQUENCE council_new_platforms_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.council_new_platforms_id_seq TO "councilbox-horus";


--
-- Name: TABLE council_platforms; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.council_platforms TO "councilbox-horus";
GRANT SELECT ON TABLE public.council_platforms TO councilbox_readonly;


--
-- Name: SEQUENCE council_platforms_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.council_platforms_id_seq TO "councilbox-horus";


--
-- Name: TABLE council_portal_attachments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.council_portal_attachments TO councilbox_readonly;


--
-- Name: TABLE council_portals; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.council_portals TO councilbox_readonly;


--
-- Name: TABLE council_statutes; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.council_statutes TO "councilbox-horus";
GRANT SELECT ON TABLE public.council_statutes TO councilbox_readonly;


--
-- Name: SEQUENCE council_statutes_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.council_statutes_id_seq TO "councilbox-horus";


--
-- Name: SEQUENCE councils_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.councils_id_seq TO "councilbox-horus";


--
-- Name: TABLE recount_info; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.recount_info TO councilbox_readonly;


--
-- Name: TABLE councils_recount; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.councils_recount TO councilbox_readonly;


--
-- Name: TABLE countries; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.countries TO "councilbox-horus";
GRANT SELECT ON TABLE public.countries TO councilbox_readonly;


--
-- Name: SEQUENCE countries_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.countries_id_seq TO "councilbox-horus";


--
-- Name: TABLE signatures; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.signatures TO "councilbox-horus";
GRANT SELECT ON TABLE public.signatures TO councilbox_readonly;


--
-- Name: TABLE dashboard_recount; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.dashboard_recount TO "councilbox-horus";
GRANT SELECT ON TABLE public.dashboard_recount TO councilbox_readonly;


--
-- Name: TABLE delegation_proxies; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.delegation_proxies TO councilbox_readonly;


--
-- Name: TABLE "documentAttachment"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."documentAttachment" TO "councilbox-horus";
GRANT SELECT ON TABLE public."documentAttachment" TO councilbox_readonly;


--
-- Name: SEQUENCE "documentAttachment_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."documentAttachment_id_seq" TO "councilbox-horus";


--
-- Name: TABLE "documentParticipants"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."documentParticipants" TO "councilbox-horus";
GRANT SELECT ON TABLE public."documentParticipants" TO councilbox_readonly;


--
-- Name: SEQUENCE "documentParticipants_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."documentParticipants_id_seq" TO "councilbox-horus";


--
-- Name: TABLE "documentSignature"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."documentSignature" TO "councilbox-horus";
GRANT SELECT ON TABLE public."documentSignature" TO councilbox_readonly;


--
-- Name: SEQUENCE "documentSignature_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."documentSignature_id_seq" TO "councilbox-horus";


--
-- Name: TABLE draft_types; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.draft_types TO "councilbox-horus";
GRANT SELECT ON TABLE public.draft_types TO councilbox_readonly;


--
-- Name: SEQUENCE draft_types_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.draft_types_id_seq TO "councilbox-horus";


--
-- Name: TABLE drafts; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.drafts TO "councilbox-horus";
GRANT SELECT ON TABLE public.drafts TO councilbox_readonly;


--
-- Name: SEQUENCE drafts_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.drafts_id_seq TO "councilbox-horus";


--
-- Name: TABLE evidence_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.evidence_logs TO councilbox_readonly;


--
-- Name: TABLE evidences; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.evidences TO councilbox_readonly;


--
-- Name: TABLE feature_exceptions; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.feature_exceptions TO councilbox_readonly;


--
-- Name: TABLE features; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.features TO "councilbox-horus";
GRANT SELECT ON TABLE public.features TO councilbox_readonly;


--
-- Name: SEQUENCE features_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.features_id_seq TO "councilbox-horus";


--
-- Name: TABLE languages; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.languages TO "councilbox-horus";
GRANT SELECT ON TABLE public.languages TO councilbox_readonly;


--
-- Name: SEQUENCE languages_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.languages_id_seq TO "councilbox-horus";


--
-- Name: TABLE "lastSendAct"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."lastSendAct" TO "councilbox-horus";
GRANT SELECT ON TABLE public."lastSendAct" TO councilbox_readonly;


--
-- Name: TABLE "lastSendConvene"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."lastSendConvene" TO "councilbox-horus";
GRANT SELECT ON TABLE public."lastSendConvene" TO councilbox_readonly;


--
-- Name: TABLE "lastSendCredentials"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."lastSendCredentials" TO "councilbox-horus";
GRANT SELECT ON TABLE public."lastSendCredentials" TO councilbox_readonly;


--
-- Name: TABLE licenses; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.licenses TO councilbox_readonly;


--
-- Name: TABLE live; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.live TO "councilbox-horus";
GRANT SELECT ON TABLE public.live TO councilbox_readonly;


--
-- Name: SEQUENCE live_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.live_id_seq TO "councilbox-horus";


--
-- Name: SEQUENCE live_participants_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.live_participants_id_seq TO "councilbox-horus";


--
-- Name: TABLE live_participants_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.live_participants_logs TO "councilbox-horus";
GRANT SELECT ON TABLE public.live_participants_logs TO councilbox_readonly;


--
-- Name: SEQUENCE live_participants_logs_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.live_participants_logs_id_seq TO "councilbox-horus";


--
-- Name: SEQUENCE live_participants_signature_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.live_participants_signature_id_seq TO "councilbox-horus";


--
-- Name: TABLE logs_participants_video; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.logs_participants_video TO "councilbox-horus";
GRANT SELECT ON TABLE public.logs_participants_video TO councilbox_readonly;


--
-- Name: SEQUENCE logs_participants_video_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.logs_participants_video_id_seq TO "councilbox-horus";


--
-- Name: TABLE "meetingCelebration"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."meetingCelebration" TO "councilbox-horus";
GRANT SELECT ON TABLE public."meetingCelebration" TO councilbox_readonly;


--
-- Name: SEQUENCE "meetingCelebration_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."meetingCelebration_id_seq" TO "councilbox-horus";


--
-- Name: TABLE "meetingConvene"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."meetingConvene" TO "councilbox-horus";
GRANT SELECT ON TABLE public."meetingConvene" TO councilbox_readonly;


--
-- Name: SEQUENCE "meetingConvene_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."meetingConvene_id_seq" TO "councilbox-horus";


--
-- Name: TABLE "meetingParticipantConnections"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."meetingParticipantConnections" TO "councilbox-horus";
GRANT SELECT ON TABLE public."meetingParticipantConnections" TO councilbox_readonly;


--
-- Name: SEQUENCE "meetingParticipantConnections_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."meetingParticipantConnections_id_seq" TO "councilbox-horus";


--
-- Name: TABLE "meetingParticipantSends"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."meetingParticipantSends" TO "councilbox-horus";
GRANT SELECT ON TABLE public."meetingParticipantSends" TO councilbox_readonly;


--
-- Name: SEQUENCE "meetingParticipantSends_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."meetingParticipantSends_id_seq" TO "councilbox-horus";


--
-- Name: TABLE "meetingParticipants"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."meetingParticipants" TO "councilbox-horus";
GRANT SELECT ON TABLE public."meetingParticipants" TO councilbox_readonly;


--
-- Name: SEQUENCE "meetingParticipants_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."meetingParticipants_id_seq" TO "councilbox-horus";


--
-- Name: TABLE "meetingPlatform"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."meetingPlatform" TO "councilbox-horus";
GRANT SELECT ON TABLE public."meetingPlatform" TO councilbox_readonly;


--
-- Name: SEQUENCE "meetingPlatform_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."meetingPlatform_id_seq" TO "councilbox-horus";


--
-- Name: TABLE meetings; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.meetings TO "councilbox-horus";
GRANT SELECT ON TABLE public.meetings TO councilbox_readonly;


--
-- Name: SEQUENCE meetings_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.meetings_id_seq TO "councilbox-horus";


--
-- Name: TABLE modal_writing_act; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.modal_writing_act TO councilbox_readonly;


--
-- Name: TABLE new_platforms; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.new_platforms TO "councilbox-horus";
GRANT SELECT ON TABLE public.new_platforms TO councilbox_readonly;


--
-- Name: SEQUENCE new_platforms_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.new_platforms_id_seq TO "councilbox-horus";


--
-- Name: TABLE organizations; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.organizations TO councilbox_readonly;


--
-- Name: TABLE "participantSms"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public."participantSms" TO "councilbox-horus";
GRANT SELECT ON TABLE public."participantSms" TO councilbox_readonly;


--
-- Name: SEQUENCE "participantSms_id_seq"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public."participantSms_id_seq" TO "councilbox-horus";


--
-- Name: TABLE participant_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.participant_logs TO "councilbox-horus";
GRANT SELECT ON TABLE public.participant_logs TO councilbox_readonly;


--
-- Name: SEQUENCE participant_logs_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.participant_logs_id_seq TO "councilbox-horus";


--
-- Name: TABLE participant_representatives; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.participant_representatives TO councilbox_readonly;


--
-- Name: TABLE participant_surveys; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.participant_surveys TO councilbox_readonly;


--
-- Name: SEQUENCE participants_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.participants_id_seq TO "councilbox-horus";


--
-- Name: TABLE pdfs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.pdfs TO "councilbox-horus";
GRANT SELECT ON TABLE public.pdfs TO councilbox_readonly;


--
-- Name: SEQUENCE pdfs_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.pdfs_id_seq TO "councilbox-horus";


--
-- Name: TABLE pg_buffercache; Type: ACL; Schema: public; Owner: azure_superuser
--

GRANT SELECT ON TABLE public.pg_buffercache TO councilbox_readonly;


--
-- Name: TABLE pg_stat_statements; Type: ACL; Schema: public; Owner: azure_superuser
--

GRANT SELECT ON TABLE public.pg_stat_statements TO councilbox_readonly;


--
-- Name: TABLE platforms; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.platforms TO "councilbox-horus";
GRANT SELECT ON TABLE public.platforms TO councilbox_readonly;


--
-- Name: SEQUENCE platforms_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.platforms_id_seq TO "councilbox-horus";


--
-- Name: TABLE poll_question_options; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.poll_question_options TO "councilbox-horus";
GRANT SELECT ON TABLE public.poll_question_options TO councilbox_readonly;


--
-- Name: SEQUENCE poll_question_options_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.poll_question_options_id_seq TO "councilbox-horus";


--
-- Name: TABLE poll_question_votings; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.poll_question_votings TO "councilbox-horus";
GRANT SELECT ON TABLE public.poll_question_votings TO councilbox_readonly;


--
-- Name: TABLE poll_question_options_recount; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.poll_question_options_recount TO "councilbox-horus";
GRANT SELECT ON TABLE public.poll_question_options_recount TO councilbox_readonly;


--
-- Name: SEQUENCE poll_question_votings_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.poll_question_votings_id_seq TO "councilbox-horus";


--
-- Name: TABLE poll_questions; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.poll_questions TO "councilbox-horus";
GRANT SELECT ON TABLE public.poll_questions TO councilbox_readonly;


--
-- Name: SEQUENCE poll_questions_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.poll_questions_id_seq TO "councilbox-horus";


--
-- Name: TABLE poll_questions_options; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.poll_questions_options TO councilbox_readonly;


--
-- Name: TABLE portal_request_attachments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.portal_request_attachments TO councilbox_readonly;


--
-- Name: TABLE provinces; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.provinces TO "councilbox-horus";
GRANT SELECT ON TABLE public.provinces TO councilbox_readonly;


--
-- Name: SEQUENCE provinces_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.provinces_id_seq TO "councilbox-horus";


--
-- Name: TABLE proxy_votes; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.proxy_votes TO councilbox_readonly;


--
-- Name: TABLE signature_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.signature_participants TO "councilbox-horus";
GRANT SELECT ON TABLE public.signature_participants TO councilbox_readonly;


--
-- Name: TABLE recount_signatures; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.recount_signatures TO "councilbox-horus";
GRANT SELECT ON TABLE public.recount_signatures TO councilbox_readonly;


--
-- Name: TABLE room_video_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.room_video_logs TO "councilbox-horus";
GRANT SELECT ON TABLE public.room_video_logs TO councilbox_readonly;


--
-- Name: SEQUENCE room_video_logs_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.room_video_logs_id_seq TO "councilbox-horus";


--
-- Name: TABLE root_statistics; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.root_statistics TO "councilbox-horus";
GRANT SELECT ON TABLE public.root_statistics TO councilbox_readonly;


--
-- Name: TABLE root_statistics_simplified; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.root_statistics_simplified TO "councilbox-horus";
GRANT SELECT ON TABLE public.root_statistics_simplified TO councilbox_readonly;


--
-- Name: TABLE scheduled_tasks; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.scheduled_tasks TO councilbox_readonly;


--
-- Name: TABLE schemas; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.schemas TO councilbox_readonly;


--
-- Name: TABLE send_attachments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.send_attachments TO councilbox_readonly;


--
-- Name: SEQUENCE sends_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.sends_id_seq TO "councilbox-horus";


--
-- Name: TABLE shareholder_request; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.shareholder_request TO councilbox_readonly;


--
-- Name: TABLE signature_attachments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.signature_attachments TO "councilbox-horus";
GRANT SELECT ON TABLE public.signature_attachments TO councilbox_readonly;


--
-- Name: SEQUENCE signature_attachments_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.signature_attachments_id_seq TO "councilbox-horus";


--
-- Name: SEQUENCE signature_participants_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.signature_participants_id_seq TO "councilbox-horus";


--
-- Name: TABLE signature_platforms; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.signature_platforms TO "councilbox-horus";
GRANT SELECT ON TABLE public.signature_platforms TO councilbox_readonly;


--
-- Name: SEQUENCE signature_platforms_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.signature_platforms_id_seq TO "councilbox-horus";


--
-- Name: SEQUENCE signatures_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.signatures_id_seq TO "councilbox-horus";


--
-- Name: TABLE simple_book_participant_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.simple_book_participant_logs TO "councilbox-horus";
GRANT SELECT ON TABLE public.simple_book_participant_logs TO councilbox_readonly;


--
-- Name: SEQUENCE simple_book_participant_logs_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.simple_book_participant_logs_id_seq TO "councilbox-horus";


--
-- Name: TABLE simple_book_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.simple_book_participants TO "councilbox-horus";
GRANT SELECT ON TABLE public.simple_book_participants TO councilbox_readonly;


--
-- Name: SEQUENCE simple_book_participants_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.simple_book_participants_id_seq TO "councilbox-horus";


--
-- Name: TABLE subdomains; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.subdomains TO councilbox_readonly;


--
-- Name: TABLE translations; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.translations TO "councilbox-horus";
GRANT SELECT ON TABLE public.translations TO councilbox_readonly;


--
-- Name: SEQUENCE translations2_id_seq1; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.translations2_id_seq1 TO "councilbox-horus";


--
-- Name: TABLE "trotter-accounts"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."trotter-accounts" TO councilbox_readonly;


--
-- Name: TABLE "trotter-descriptors"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."trotter-descriptors" TO councilbox_readonly;


--
-- Name: TABLE "trotter-evidences"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."trotter-evidences" TO councilbox_readonly;


--
-- Name: TABLE "trotter-feature_exceptions"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."trotter-feature_exceptions" TO councilbox_readonly;


--
-- Name: TABLE "trotter-features"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."trotter-features" TO councilbox_readonly;


--
-- Name: TABLE "trotter-organizations"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."trotter-organizations" TO councilbox_readonly;


--
-- Name: TABLE "trotter-trace_descriptors"; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public."trotter-trace_descriptors" TO councilbox_readonly;


--
-- Name: TABLE user_company; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.user_company TO "councilbox-horus";
GRANT SELECT ON TABLE public.user_company TO councilbox_readonly;


--
-- Name: SEQUENCE user_company_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.user_company_id_seq TO "councilbox-horus";


--
-- Name: TABLE user_councils; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.user_councils TO "councilbox-horus";
GRANT SELECT ON TABLE public.user_councils TO councilbox_readonly;


--
-- Name: TABLE user_sends; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.user_sends TO "councilbox-horus";
GRANT SELECT ON TABLE public.user_sends TO councilbox_readonly;


--
-- Name: SEQUENCE user_sends_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.user_sends_id_seq TO "councilbox-horus";


--
-- Name: TABLE user_signatures; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.user_signatures TO "councilbox-horus";
GRANT SELECT ON TABLE public.user_signatures TO councilbox_readonly;


--
-- Name: TABLE user_solpheouser; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.user_solpheouser TO "councilbox-horus";
GRANT SELECT ON TABLE public.user_solpheouser TO councilbox_readonly;


--
-- Name: SEQUENCE user_solpheouser_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.user_solpheouser_id_seq TO "councilbox-horus";


--
-- Name: TABLE users; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.users TO "councilbox-horus";
GRANT SELECT ON TABLE public.users TO councilbox_readonly;


--
-- Name: SEQUENCE users_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.users_id_seq TO "councilbox-horus";


--
-- Name: TABLE v_act_sends; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.v_act_sends TO "councilbox-horus";
GRANT SELECT ON TABLE public.v_act_sends TO councilbox_readonly;


--
-- Name: TABLE v_convene_sends; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.v_convene_sends TO "councilbox-horus";
GRANT SELECT ON TABLE public.v_convene_sends TO councilbox_readonly;


--
-- Name: TABLE v_council_participants_convene; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.v_council_participants_convene TO councilbox_readonly;


--
-- Name: TABLE v_credentials_sends; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.v_credentials_sends TO "councilbox-horus";
GRANT SELECT ON TABLE public.v_credentials_sends TO councilbox_readonly;


--
-- Name: TABLE v_live_participants_credentials; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.v_live_participants_credentials TO councilbox_readonly;


--
-- Name: TABLE votings; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.votings TO "councilbox-horus";
GRANT SELECT ON TABLE public.votings TO councilbox_readonly;


--
-- Name: TABLE vagenda_quality_vote; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vagenda_quality_vote TO "councilbox-horus";
GRANT SELECT ON TABLE public.vagenda_quality_vote TO councilbox_readonly;


--
-- Name: TABLE vcensus_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.vcensus_participants TO councilbox_readonly;


--
-- Name: TABLE vcensus_users; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vcensus_users TO "councilbox-horus";
GRANT SELECT ON TABLE public.vcensus_users TO councilbox_readonly;


--
-- Name: TABLE vcomments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vcomments TO "councilbox-horus";
GRANT SELECT ON TABLE public.vcomments TO councilbox_readonly;


--
-- Name: TABLE vcompanies; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vcompanies TO "councilbox-horus";
GRANT SELECT ON TABLE public.vcompanies TO councilbox_readonly;


--
-- Name: TABLE vcompanies_light; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vcompanies_light TO "councilbox-horus";
GRANT SELECT ON TABLE public.vcompanies_light TO councilbox_readonly;


--
-- Name: TABLE vcouncil_attachments; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vcouncil_attachments TO "councilbox-horus";
GRANT SELECT ON TABLE public.vcouncil_attachments TO councilbox_readonly;


--
-- Name: TABLE vcouncil_corporates; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vcouncil_corporates TO "councilbox-horus";
GRANT SELECT ON TABLE public.vcouncil_corporates TO councilbox_readonly;


--
-- Name: TABLE vcouncil_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.vcouncil_participants TO councilbox_readonly;


--
-- Name: TABLE vcouncil_participants_councils; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vcouncil_participants_councils TO "councilbox-horus";
GRANT SELECT ON TABLE public.vcouncil_participants_councils TO councilbox_readonly;


--
-- Name: TABLE vcouncil_participants_with_representatives; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.vcouncil_participants_with_representatives TO councilbox_readonly;


--
-- Name: TABLE vcouncils; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.vcouncils TO councilbox_readonly;


--
-- Name: TABLE vcurrent_liveparticipants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vcurrent_liveparticipants TO "councilbox-horus";
GRANT SELECT ON TABLE public.vcurrent_liveparticipants TO councilbox_readonly;


--
-- Name: TABLE video_votings_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.video_votings_logs TO "councilbox-horus";
GRANT SELECT ON TABLE public.video_votings_logs TO councilbox_readonly;


--
-- Name: SEQUENCE video_votings_logs_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.video_votings_logs_id_seq TO "councilbox-horus";


--
-- Name: TABLE vlive_participants_connections; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vlive_participants_connections TO "councilbox-horus";
GRANT SELECT ON TABLE public.vlive_participants_connections TO councilbox_readonly;


--
-- Name: TABLE vlive_participants_councils; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vlive_participants_councils TO "councilbox-horus";
GRANT SELECT ON TABLE public.vlive_participants_councils TO councilbox_readonly;


--
-- Name: TABLE vlive_participants_logs; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vlive_participants_logs TO "councilbox-horus";
GRANT SELECT ON TABLE public.vlive_participants_logs TO councilbox_readonly;


--
-- Name: TABLE vlive_participants_to_delegate; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.vlive_participants_to_delegate TO councilbox_readonly;


--
-- Name: TABLE vlive_participants_with_representatives; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.vlive_participants_with_representatives TO councilbox_readonly;


--
-- Name: TABLE vlogs_participants_video; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vlogs_participants_video TO "councilbox-horus";
GRANT SELECT ON TABLE public.vlogs_participants_video TO councilbox_readonly;


--
-- Name: TABLE vote_letters; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.vote_letters TO councilbox_readonly;


--
-- Name: SEQUENCE votings_id_seq; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON SEQUENCE public.votings_id_seq TO "councilbox-horus";


--
-- Name: TABLE votings_live; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.votings_live TO councilbox_readonly;


--
-- Name: TABLE votings_live_fullname; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.votings_live_fullname TO councilbox_readonly;


--
-- Name: TABLE vquestion_votings_of_nominal; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vquestion_votings_of_nominal TO "councilbox-horus";
GRANT SELECT ON TABLE public.vquestion_votings_of_nominal TO councilbox_readonly;


--
-- Name: TABLE vsimple_book_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vsimple_book_participants TO "councilbox-horus";
GRANT SELECT ON TABLE public.vsimple_book_participants TO councilbox_readonly;


--
-- Name: TABLE vuser_agendas; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vuser_agendas TO "councilbox-horus";
GRANT SELECT ON TABLE public.vuser_agendas TO councilbox_readonly;


--
-- Name: TABLE vuser_participants; Type: ACL; Schema: public; Owner: councilbox
--

GRANT ALL ON TABLE public.vuser_participants TO "councilbox-horus";
GRANT SELECT ON TABLE public.vuser_participants TO councilbox_readonly;


--
-- Name: TABLE vvotings; Type: ACL; Schema: public; Owner: councilbox
--

GRANT SELECT ON TABLE public.vvotings TO councilbox_readonly;


--
-- PostgreSQL database dump complete
--

